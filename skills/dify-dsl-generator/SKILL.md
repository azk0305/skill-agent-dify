---
name: dify-dsl-generator
description: >
  Dify ワークフローのDSL（YMLファイル）を生成・編集・検証するスキル。
  以下の場面で使用する：
  - ビジネス要件からDifyワークフローDSLを新規生成したい（「〜のDSLを作って」「〜のワークフローを生成して」）
  - 既存のDSLを編集・ノード追加・修正したい（「このDSLに〜を追加して」「〜ノードを変更して」）
  - DSLの構文チェックやバリデーションをしたい（「このDSLを確認して」「検証して」）
  - 特定ノードの設定方法・フィールドを確認したい（「〜ノードの書き方は？」「〜フィールドの意味は？」）
version: 2.0.0
---

# Dify DSL ジェネレーター

Dify ワークフローDSL（YMLファイル）の生成・編集・検証・参照を行うスキル。

---

## 4つの動作モード

| モード | トリガー例 | 処理内容 |
|--------|-----------|---------|
| **生成** | 「〜のDSLを作って」「〜ワークフローを生成して」 | 要件分析 → ノード設計 → 完全YAML出力 |
| **編集** | 「このDSLに〜を追加して」「〜ノードを変更して」 | 既存DSL解析 → 変更箇所特定 → 完全YAML出力 |
| **検証** | 「このDSLを確認して」「バリデーションして」 | チェックリスト実行 → 問題列挙 → 修正提示 |
| **参照** | 「〜ノードの書き方は？」「〜フィールドの意味は？」 | node-reference.md を参照して回答 |

---

## DSLファイルの基本構造

```yaml
app:
  description: 'ワークフローの説明'
  icon: 🤖
  icon_background: '#FFEAD5'
  mode: advanced-chat    # advanced-chat / workflow / agent-chat
  name: ワークフロー名
  use_icon_as_answer_icon: false

dependencies:
- current_identifier: null
  type: marketplace
  value:
    marketplace_plugin_unique_identifier: 'プラグインID:バージョン@ハッシュ'

kind: app
version: 0.3.0

workflow:
  conversation_variables: []
  environment_variables: []
  features:
    file_upload:
      enabled: false
      # ... 省略可
    speech_to_text:
      enabled: false
    text_to_speech:
      enabled: false
  graph:
    edges:    # ノード間の接続
    - ...
    nodes:    # ノード定義
    - ...
    viewport:
      x: 0
      y: 0
      zoom: 1
```

**モード（`app.mode`）の選択:**
- `advanced-chat`: 複数ターン対話。Answerノードで回答を返す
- `workflow`: 単発タスク処理。Endノードで終了
- `agent-chat`: ツールを自律的に呼び出すAIエージェント

---

## ノードタイプ一覧と基本記法

### ノード共通フィールド（必須）

```yaml
- data:
    desc: ''
    selected: false    # dataレベルにも必要
    title: ノード名
    type: <ノードタイプ>
    # ノード固有フィールド...
  height: 90
  id: '1700000000001'    # 数値タイムスタンプ（一意）
  position:
    x: 100
    y: 300
  positionAbsolute:      # ※省略不可。position と同じ値
    x: 100
    y: 300
  selected: false        # ノードレベルにも必要
  sourcePosition: right  # 固定値
  targetPosition: left   # 固定値
  type: custom           # 固定値
  width: 244
```

### Start ノード

```yaml
data:
  type: start
  variables:
  - label: ユーザー入力
    max_length: 1000
    options: []
    required: true
    type: paragraph    # paragraph / text-input / select / number / file / files
    variable: query
```

### LLM ノード

```yaml
data:
  type: llm
  context:
    enabled: false
    variable_selector: []
  model:
    completion_params:
      temperature: 0.7
      max_tokens: 2000
    mode: chat
    name: claude-sonnet-4-6
    provider: anthropic
  prompt_template:
  - id: <UUID>
    role: system
    text: システムプロンプト
  - id: <UUID>
    role: user
    text: '{{#nodeId.varName#}}'
  variables: []
  vision:
    enabled: false
```

### Code ノード

```yaml
data:
  type: code
  code: |
    def main(arg1: str) -> dict:
        return {"result": arg1}
  code_language: python3
  outputs:
    result:
      children: null
      type: string
  variables:
  - value_selector:
    - 'sourceNodeId'
    - variableName
    variable: arg1
```

### HTTP Request ノード

```yaml
data:
  type: http-request
  authorization:
    config: null
    type: no-auth
  body:
    data: '{"key": "{{#nodeId.varName#}}"}'
    type: json
  headers: ''
  method: post
  timeout:
    max_connect_timeout: 0
    max_read_timeout: 0
    max_write_timeout: 0
  url: https://api.example.com/endpoint
  variables: []
```

### If-Else ノード

```yaml
data:
  type: if-else
  cases:
  - case_id: case1
    conditions:
    - comparison_operator: contains    # is / is not / contains / not contains 等
      id: <UUID>
      value: '期待値'
      variable_selector:
      - 'nodeId'
      - variableName
    id: case1
    logical_operator: and
  logical_operator: or
height: 126    # If-Elseは126
```

### Parameter Extractor ノード

```yaml
data:
  type: parameter-extractor
  instruction: 抽出指示の説明
  is_array: false
  model:
    completion_params: {}
    mode: chat
    name: claude-sonnet-4-6
    provider: anthropic
  parameters:
  - description: パラメータの説明
    name: param_name
    required: true
    type: string
  query:              # ※配列形式（{role,text}形式は誤り）
  - 'sourceNodeId'   # ノードID
  - variableName     # 変数名
  reasoning_mode: prompt
  variables: []
  vision:
    enabled: false
```

### Variable Aggregator ノード

```yaml
data:
  type: variable-aggregator
  advanced_settings: null
  groups:
  - group_name: グループ1
    output_type: string
    variables:
    - value_selector:
      - 'nodeId'
      - variableName
      variable: output_var
```

### Tool ノード

```yaml
data:
  type: tool
  provider_id: bowenliang123/md_exporter/md_exporter
  provider_name: bowenliang123/md_exporter/md_exporter
  provider_type: builtin
  tool_configurations: {}
  tool_label: ツールラベル
  tool_name: tool_function_name
  tool_parameters:
    param_name:
      type: mixed
      value: '{{#nodeId.varName#}}'
```

### Answer ノード

```yaml
data:
  type: answer
  answer: '{{#nodeId.text#}}'
  variables: []
height: 143    # ※Answerノードのみ143
```

---

## エッジ接続ルール

### 基本形式

```yaml
edges:
- data:
    isInIteration: false
    isInLoop: false
    sourceType: start    # 接続元ノードのtype
    targetType: llm      # 接続先ノードのtype
  id: '{sourceId}-source-{targetId}-target'    # ※このフォーマット厳守
  source: 'sourceNodeId'
  sourceHandle: source    # 通常は 'source'
  target: 'targetNodeId'
  targetHandle: target    # 固定値
  type: custom
  zIndex: 0
```

### If-Else 分岐エッジの `sourceHandle`

| sourceHandle | 意味 |
|-------------|------|
| `case1` | 最初の条件が真 |
| `case2` | 2番目の条件が真 |
| `false` | すべての条件が偽（else） |

**If-Else エッジ例:**
```yaml
# 成功ブランチ（case1 = 真）
- id: ifelse-source-nodeA-target
  source: 'ifelse'
  sourceHandle: case1
  target: 'nodeA'
  targetHandle: target

# エラーブランチ（false = else）
- id: ifelse-source-nodeB-target
  source: 'ifelse'
  sourceHandle: false
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
| `{{#sys.files#}}` | アップロードされたファイル |
| `{{#sys.conversation_id#}}` | 会話ID |
| `{{#sys.user_id#}}` | ユーザーID |

### ノード別の主要出力変数

| ノードタイプ | 変数名 | 説明 |
|------------|--------|------|
| LLM | `text` | LLMの出力テキスト |
| Code | 定義した名前 | `outputs` で定義した変数 |
| HTTP Request | `body` | レスポンスボディ |
| HTTP Request | `status_code` | HTTPステータスコード |
| Parameter Extractor | パラメータ名 | 抽出したパラメータ値 |
| Tool | `files` | ツールが返すファイル |
| Start | 定義した変数名 | `variables` で定義した入力 |

---

## モード別処理手順

### モード1: DSL生成

1. **要件分析**: ユーザーの要件から必要なノードとフローを特定
2. **モード選択**: chatflow（対話型）か workflow（単発処理）か判断
3. **ノード設計**:
   - Startの入力変数を定義
   - LLMプロンプトを業務要件に合わせて設計
   - 必要なCode/HTTP/Tool ノードを追加
4. **レイアウト設計**: X座標を300px間隔で配置（Start: 80, 次: 380, ...）
5. **完全YAML出力**: インポート可能な完全なYAMLを出力

### モード2: DSL編集

1. 提供された既存DSLを解析
2. ユーザーが要求する変更箇所を特定
3. 影響するエッジも含めて更新
4. 変更後の完全なYAMLを出力（差分だけでなく全体を出力）

### モード3: DSL検証

以下の**バリデーションチェックリスト**を実行:

**構造チェック:**
- [ ] `kind: app` と `version: 0.3.0` が存在する
- [ ] `app.mode` が有効な値（advanced-chat/workflow/agent-chat）
- [ ] `workflow.graph.nodes` に start ノードが存在する
- [ ] `workflow.graph.nodes` に answer/end ノードが存在する

**ノードチェック:**
- [ ] 全ノードに一意の `id` がある
- [ ] 全ノードに `positionAbsolute` フィールドがある（`position` と同じ値）
- [ ] 全ノードに `selected: false`（dataレベル・ノードレベル両方）
- [ ] 全ノードに `sourcePosition: right` と `targetPosition: left`
- [ ] Answer ノードの `height` が `143`

**エッジチェック:**
- [ ] エッジIDのフォーマットが `{sourceId}-source-{targetId}-target`
- [ ] `sourceType` と `targetType` が実際のノードタイプと一致
- [ ] 孤立したノード（エッジ未接続）がない
- [ ] すべてのノードが start → answer/end のパスに接続されている

**変数参照チェック:**
- [ ] `{{#nodeId.varName#}}` の形式が正しい（`{{nodeId.varName}}` は誤り）
- [ ] 参照先のノードIDが実際に存在する
- [ ] Parameter Extractor の `query` が配列形式（`['nodeId', 'varName']`）

**Parameter Extractor チェック:**
- [ ] `query` が `[ノードID, 変数名]` の配列形式（`{role, text}` 形式は誤り）

### モード4: ノード参照

`references/node-reference.md` を参照して、ノードの設定方法やフィールドの詳細を回答する。

---

## よくある間違いと修正

### ❌ エラー1: 変数参照の形式が間違い

```yaml
# 誤
text: '{{nodeId.varName}}'
text: '{{ #nodeId.varName# }}'

# 正
text: '{{#nodeId.varName#}}'
```

### ❌ エラー2: positionAbsolute が欠落

```yaml
# 誤（positionAbsoluteなし）
- id: 'node1'
  position:
    x: 100
    y: 300
  type: custom

# 正
- id: 'node1'
  position:
    x: 100
    y: 300
  positionAbsolute:    # ← 必須
    x: 100
    y: 300
  type: custom
```

### ❌ エラー3: Parameter Extractor の query 形式が間違い

```yaml
# 誤（古い形式）
query:
- role: user
  text: '{{#nodeId.varName#}}'

# 正（配列形式）
query:
- 'nodeId'
- variableName
```

### ❌ エラー4: If-Else エッジの sourceHandle が間違い

```yaml
# 誤（elseブランチ）
sourceHandle: else

# 正（elseブランチ）
sourceHandle: false
```

### ❌ エラー5: Answer ノードの height が間違い

```yaml
# 誤
height: 90

# 正（Answerノードのみ）
height: 143
```

---

## 詳細参照先

- **ノード設定の詳細仕様**: [references/node-reference.md](./references/node-reference.md)
- **最小構成の例（Start→LLM→Answer）**: [examples/simple-chatflow.yml](./examples/simple-chatflow.yml)
- **条件分岐を含む複合例**: [examples/data-query-workflow.yml](./examples/data-query-workflow.yml)
