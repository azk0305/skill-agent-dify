# Dify ノード設定リファレンス

Dify DSL の各ノードタイプの完全なフィールド仕様。

---

## 共通フィールド（全ノード必須）

```yaml
- data:
    desc: ''          # ノードの説明（省略可、空文字可）
    selected: false   # 選択状態（常にfalse）
    title: ノード名   # UI表示名
    type: <ノードタイプ>
    # ノード固有フィールド...
  height: 90          # ノードの高さ（answerは143）
  id: '一意のID'      # 数値タイムスタンプ推奨（例: '1747991890414'）
  position:
    x: 100
    y: 300
  positionAbsolute:   # ※省略不可。position と同じ値を設定
    x: 100
    y: 300
  selected: false     # ノードレベルでも必須（dataレベルと別途必要）
  sourcePosition: right   # 固定値
  targetPosition: left    # 固定値
  type: custom            # 固定値
  width: 244
```

---

## Start ノード

```yaml
data:
  desc: ''
  selected: false
  title: 開始
  type: start
  variables:
  - label: ユーザー入力
    max_length: 1000
    options: []       # selectタイプのみ選択肢を列挙
    required: true
    type: paragraph   # 変数タイプ（下記参照）
    variable: query   # 変数名（変数参照時に使用）
```

**変数タイプ（`type` の値）:**

| 値 | 説明 |
|----|------|
| `paragraph` | 複数行テキスト |
| `text-input` | 単一行テキスト |
| `select` | ドロップダウン選択 |
| `number` | 数値 |
| `file` | ファイルアップロード（単一） |
| `files` | ファイルアップロード（複数） |

**fileタイプの追加フィールド:**
```yaml
- allowed_file_extensions:
  - .JPG
  - .PNG
  - .PDF
  allowed_file_types:
  - image      # image, document, audio, video
  allowed_file_upload_methods:
  - local_file
  - remote_url
  label: ファイル
  max_length: 48
  options: []
  required: true
  type: file
  variable: file
```

---

## LLM ノード

```yaml
data:
  context:
    enabled: false
    variable_selector: []
  desc: ''
  model:
    completion_params:
      temperature: 0.7    # 0.0〜2.0
      max_tokens: 2000
    mode: chat            # chat または completion
    name: claude-sonnet-4-6
    provider: anthropic   # openai, anthropic, 等
  prompt_template:
  - id: <UUID形式のID>    # 例: e9b9ec0c-5498-4e17-a000-f3b06a59e0d0
    role: system
    text: システムプロンプト
  - id: <UUID形式のID>
    role: user
    text: ユーザープロンプト {{#nodeId.varName#}}
  selected: false
  title: LLMノード名
  type: llm
  variables: []
  vision:
    enabled: false
```

**visionを有効化する場合:**
```yaml
vision:
  configs:
    detail: high    # low, high, auto
    variable_selector:
    - 'sourceNodeId'   # ノードID（文字列の配列）
    - variableName     # 変数名
  enabled: true
```

**よく使うプロバイダー:**

| provider | 説明 |
|----------|------|
| `anthropic` | Claude |
| `openai` | OpenAI |
| `langgenius/openai_api_compatible/openai_api_compatible` | OpenAI互換 |
| `alibaba` | 通義千問 |

---

## Code ノード

```yaml
data:
  code: |
    def main(arg1: str, arg2: str) -> dict:
        # 処理ロジック
        return {
            "result": arg1 + arg2,
            "status": "success"
        }
  code_language: python3    # python3 または javascript
  desc: ''
  outputs:
    result:
      children: null
      type: string    # 出力型（下記参照）
    status:
      children: null
      type: string
  selected: false
  title: コード実行
  type: code
  variables:
  - value_selector:
    - 'sourceNodeId'   # 参照元ノードID
    - variableName     # 参照する変数名
    variable: arg1     # コード内の引数名
```

**出力型（`outputs` の `type`）:**

| 値 | 説明 |
|----|------|
| `string` | 文字列 |
| `number` | 数値 |
| `object` | オブジェクト |
| `array[string]` | 文字列配列 |
| `array[number]` | 数値配列 |
| `array[object]` | オブジェクト配列 |

---

## HTTP Request ノード

```yaml
data:
  authorization:
    config: null
    type: no-auth    # no-auth, api-key, bearer
  body:
    data: '{"key": "{{#nodeId.varName#}}"}'
    type: json       # none, json, form-data, x-www-form-urlencoded, raw-text
  desc: ''
  headers: 'Content-Type: application/json'
  method: post       # get, post, put, patch, delete, head
  selected: false
  timeout:
    max_connect_timeout: 0
    max_read_timeout: 0
    max_write_timeout: 0
  title: HTTPリクエスト
  type: http-request
  url: https://api.example.com/endpoint
  variables: []
```

**API Key認証の場合:**
```yaml
authorization:
  config:
    api_key: '{{#env.API_KEY#}}'
    header: 'Authorization'
  type: api-key
```

---

## If-Else ノード

```yaml
data:
  cases:
  - case_id: case1
    conditions:
    - comparison_operator: contains
      id: <UUID形式のID>
      value: 期待値
      variable_selector:
      - 'nodeId'
      - variableName
    id: case1
    logical_operator: and    # and, or
  logical_operator: or       # 複数caseの結合: and, or
  desc: ''
  selected: false
  title: 条件分岐
  type: if-else
```

**If-Elseエッジの `sourceHandle` 値:**

| 値 | 対応するブランチ |
|----|----------------|
| `case1` | 最初のcaseが真の場合 |
| `case2` | 2番目のcaseが真の場合 |
| `false` | いずれのcaseも偽の場合（else） |

**比較演算子（`comparison_operator`）:**

| 値 | 説明 |
|----|------|
| `is` | 等しい |
| `is not` | 等しくない |
| `contains` | 含む |
| `not contains` | 含まない |
| `start with` | で始まる |
| `end with` | で終わる |
| `is empty` | 空 |
| `is not empty` | 空でない |
| `>` `<` `>=` `<=` | 数値比較 |

---

## Tool ノード

```yaml
data:
  desc: ''
  is_team_authorization: true
  output_schema: null
  provider_id: bowenliang123/md_exporter/md_exporter
  provider_name: bowenliang123/md_exporter/md_exporter
  provider_type: builtin    # builtin, api, plugin
  selected: false
  title: ツール呼び出し
  tool_configurations: {}
  tool_description: ツールの説明
  tool_label: ツールラベル
  tool_name: tool_function_name
  tool_parameters:
    param_name:
      type: mixed    # mixed, string, number, boolean
      value: '{{#nodeId.varName#}}'
  type: tool
```

---

## Parameter Extractor ノード

```yaml
data:
  desc: ''
  instruction: 抽出指示の説明
  is_array: false    # 配列として抽出するか
  model:
    completion_params:
      temperature: 0.7
    mode: chat
    name: claude-sonnet-4-6
    provider: anthropic
  parameters:
  - description: パラメータの説明
    name: param_name
    required: true
    type: string    # string, number, boolean, object, array
  query:            # ※重要: ノードIDと変数名の配列形式
  - 'sourceNodeId'  # 参照元ノードID（文字列）
  - variableName    # 参照する変数名（文字列）
  reasoning_mode: prompt    # prompt または function_call
  selected: false
  title: パラメータ抽出
  type: parameter-extractor
  variables: []
  vision:
    enabled: false
```

> **注意:** `query` フィールドは `[ノードID, 変数名]` の2要素配列。
> 古いドキュメントにある `{role, text}` 形式は**誤り**。

---

## Variable Aggregator ノード

```yaml
data:
  advanced_settings: null
  desc: ''
  groups:
  - group_name: グループ1
    output_type: string    # string, number, object, array[string]
    variables:
    - value_selector:
      - 'nodeId'
      - variableName
      variable: output_var_name
  selected: false
  title: 変数アグリゲーター
  type: variable-aggregator
```

---

## Answer ノード

```yaml
data:
  answer: |
    {{#nodeId.text#}}

    静的テキストと変数参照を組み合わせ可能
  desc: ''
  selected: false
  title: 回答
  type: answer
  variables: []
height: 143    # ※answerノードは143
```

---

## エッジ（Edges）の設定

```yaml
edges:
- data:
    isInIteration: false
    isInLoop: false
    sourceType: start         # 接続元ノードのtype
    targetType: llm           # 接続先ノードのtype
  id: '{sourceId}-source-{targetId}-target'   # IDフォーマット
  source: 'sourceNodeId'
  sourceHandle: source        # 通常は 'source'（If-Elseは 'case1', 'false' 等）
  target: 'targetNodeId'
  targetHandle: target        # 固定値 'target'
  type: custom                # 固定値
  zIndex: 0
```

**If-Elseの分岐エッジ例:**
```yaml
# case1（真）の分岐
- id: ifelse1-source-nodeA-target
  source: 'ifelse1'
  sourceHandle: case1    # 条件が真の場合
  target: 'nodeA'
  targetHandle: target

# false（偽/else）の分岐
- id: ifelse1-source-nodeB-target
  source: 'ifelse1'
  sourceHandle: false    # いずれの条件も偽の場合
  target: 'nodeB'
  targetHandle: target
```

---

## 変数参照システム

### 基本形式

```
{{#nodeId.variableName#}}
```

### システム変数

| 変数 | 説明 |
|------|------|
| `{{#sys.query#}}` | ユーザー入力テキスト |
| `{{#sys.files#}}` | ユーザーがアップロードしたファイル |
| `{{#sys.conversation_id#}}` | 会話ID |
| `{{#sys.user_id#}}` | ユーザーID |

### ノード別の主要出力変数

| ノードタイプ | 変数名 | 説明 |
|------------|--------|------|
| LLM | `text` | LLMの出力テキスト |
| Code | 定義した変数名 | outputs に定義した変数 |
| HTTP Request | `body` | レスポンスボディ |
| HTTP Request | `status_code` | HTTPステータスコード |
| Parameter Extractor | 定義したパラメータ名 | 抽出したパラメータ |
| Tool | `files` | ツールが返すファイル |
| Variable Aggregator | グループのoutput_type変数 | 集約された値 |

---

## 座標レイアウトガイド

### 推奨レイアウト（水平フロー）

```
Start(80,282) → Node1(380,282) → Node2(680,282) → Answer(980,282)
              間隔: 300px
```

### 分岐レイアウト

```
                     → Branch1(900,150)
IfElse(600,282) →
                     → Branch2(900,450)
                     ↓
              VarAggregator(1200,282) → Answer(1500,282)
```

### ノードサイズ

| ノードタイプ | width | height |
|------------|-------|--------|
| Start | 244 | 90 |
| LLM | 244 | 90 |
| Code | 244 | 90 |
| HTTP Request | 244 | 90 |
| If-Else | 244 | 126 |
| Tool | 244 | 54 |
| Parameter Extractor | 244 | 90 |
| Variable Aggregator | 244 | 90 |
| Answer | 244 | **143** |
