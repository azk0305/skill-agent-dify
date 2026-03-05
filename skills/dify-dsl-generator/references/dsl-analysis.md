# Dify DSL Reference and Cheat Sheet

## Overview

This document summarizes the analysis conducted to generate workflow and chatflow ideas for Dify DSL, focusing on:
1. Major functional nodes
2. External integration nodes
3. Business use cases

## Dify DSL Patterns Investigation

Through investigation of Dify's official documentation and related resources, we analyzed typical usage patterns for workflows and chatflows, common node combinations, and business use cases, with special focus on advanced patterns involving complex conditional branching, human intervention, external API integration, and data processing.

## Basic Structure of the DSL

The Dify app's DSL is primarily composed of metadata and workflow definitions for the app.

```yaml
app:
  name: My App Name
  description: App description
  icon: 🤖
  icon_background: '#D5F5F6'
  mode: advanced-chat # or workflow
dependencies: []
kind: app
version: 0.3.0
workflow:
  conversation_variables:
  environment_variables:
  features: {}
  graph:
    nodes:
    edges:
    viewport: {}
  rag_pipeline_variables: []

```

## Workflow DSL Structure and Node Types Understanding

### Basic Structure
- `app`: Contains application metadata (description, icon, mode, name)
- `dependencies`: Lists required marketplace plugins
- `kind`: Specifies app type (always "app")
- `version`: DSL version
- `workflow`: Contains conversation/environment variables, features, and graph

### Modes
- `advanced-chat`: Multi-turn conversations with Answer nodes
- `workflow`: Single-task processing with End nodes

### Node Types
1. **Start Node**: Entry point with user input variables
2. **LLM Node**: Invokes language models for text generation/analysis
3. **Code Node**: Executes Python/JavaScript for data processing
4. **HTTP Request Node**: Connects to external APIs
5. **If-Else Node**: Adds conditional logic and branching
6. **Parameter Extractor Node**: Converts natural language to structured data
7. **Variable Aggregator Node**: Combines variables from different branches
8. **Tool Node**: Connects to external services via pre-built integrations
9. **Answer Node**: Defines response content in chatflows
10. **Human Input Node**: Pauses workflows for human review/decision
11. **Template Transform Node**: Formats data using Jinja2 templating
12. **Knowledge Retrieval Node**: Accesses knowledge bases for RAG
13. **Question Classifier Node**: Categorizes user input for routing
14. **List Operator Node**: Filters/sorts/selects array elements
15. **Document Extractor Node**: Extracts text from uploaded documents
16. **Variable Assigner Node**: Manages persistent conversation variables
17. **Iteration Node**: Processes arrays with independent sub-workflows
18. **Loop Node**: Executes repetitive workflows with progressive refinement
19. **Trigger Nodes**: Webhook/Schedule triggers for event-based workflows

## Chatflow DSL Characteristics and Examples

Chatflows are designed specifically for conversational scenarios:
- Use `advanced-chat` mode
- Include Answer nodes instead of End nodes
- Typically involve multi-turn dialogues
- Often incorporate knowledge retrieval for contextual responses
- May include question classification for intelligent routing

## Advanced Pattern Identification

### Complex Conditional Branching
- Multi-level decision trees with multiple If-Else nodes
- Dynamic routing based on user identity or context
- Specialized knowledge base selection per question type
- Context-aware processing strategies

### Human Intervention Mechanisms
- Human Input nodes for approval workflows
- Custom delivery methods (web app/email notifications)
- Timeout policies to prevent indefinite suspension
- Multiple action buttons with different styles

### External API Integration
- HTTP Request nodes for connecting to any HTTP service
- All standard HTTP methods supported
- Dynamic variable replacement in URLs/headers/body
- Automatic file response detection and handling
- Retry mechanisms and error handling paths

### Data Processing Capabilities
- Code nodes for custom Python/JavaScript processing
- List Operator nodes for array manipulation
- Parameter Extractor nodes for natural language to structured data
- Template Transform nodes for data formatting
- Variable Assigner nodes for persistent storage

## Major Functional Nodes Detailed Investigation

### Parameter Extractor
- Converts unstructured text to structured data using LLM
- Bridges natural language input with structured parameters
- Business use cases: contact info extraction, product specs parsing, API parameter conversion
- Typical combinations: User Input → Parameter Extractor → Tool/HTTP Request

### Variable Aggregator
- Combines variables from different execution paths
- Maps variables with same function from different branches
- Business use cases: consolidating results from data sources, merging classification outputs
- Typical combinations: Conditional Branches → Different Paths → Variable Aggregator

### Template Transform
- Formats data using Jinja2 templating
- Combines variables from multiple sources
- Business use cases: report generation, email templates, formatted API responses
- Typical combinations: Data Extraction → Template Transform → Formatted Output

### Knowledge Retrieval
- Connects to knowledge bases for RAG
- Supports scoring thresholds and top-K results
- Business use cases: customer support, legal document analysis, technical support
- Typical combinations: User Query → Knowledge Retrieval → LLM (with context)

### Question Classifier
- Classifies user queries into predefined categories
- Uses LLM for intelligent classification
- Business use cases: inquiry routing, ticket categorization, knowledge base selection
- Typical combinations: User Input → Question Classifier → Conditional Routing

### Code Node
- Executes Python/JavaScript for complex transformations
- Supports external libraries (NumPy, Pandas, Lodash)
- Business use cases: calculations, data cleaning, custom business logic
- Typical combinations: Data Extraction → Code (Processing) → Downstream Nodes

### List Operator
- Processes arrays (filter, sort, select)
- Handles mixed file types and complex arrays
- Business use cases: document filtering, product sorting, result selection
- Typical combinations: File Upload → List Operator → Iteration

### Document Extractor
- Extracts text content from documents
- Supports multiple file formats
- Business use cases: contract processing, research paper analysis, scanned document conversion
- Typical combinations: File Upload → Document Extractor → LLM Analysis

### Variable Assigner
- Assigns values to writable variables
- Enables cross-conversation data retention
- Business use cases: user preference storage, session state maintenance
- Typical combinations: User Input → Variable Assigner → Conversation Storage

### Iteration
- Processes array elements independently
- Executes sub-workflows for each element
- Business use cases: multi-platform content generation, batch customer data processing
- Typical combinations: List Operator → Iteration → Processing

### Loop
- Executes repetitive workflows with progressive refinement
- Each iteration depends on previous results
- Business use cases: content refinement, parameter optimization, deep data analysis
- Typical combinations: Initial Processing → Loop (Refinement) → Final Output

## External Integration Nodes Detailed Investigation

### HTTP Request
- Connects to external APIs and web services
- Full HTTP method support with configurable headers/body
- Authentication support (API keys, OAuth)
- Business use cases: CRM integration, real-time data fetching, notification sending
- Typical combinations: User Input → HTTP Request → Data Processing

### Tool
- Executes predefined tools with specific functionalities
- Credential management for secure connections
- Business use cases: note saving, external search, communication platform integration
- Typical combinations: User Request → Tool (Execution) → Response

### Plugin
- Extends Dify functionality through custom plugins
- Encapsulates complex business logic
- Business use cases: enterprise system connection, legacy system integration
- Typical combinations: Custom Processing Requirements → Plugin → Specialized Output

### Webhook Trigger
- Starts workflows in response to external events
- Security validation with signatures/HMAC
- Business use cases: payment notifications, GitHub events, social media mentions
- Typical combinations: External Event → Webhook Trigger → Processing Workflow

### Schedule Trigger
- Initiates workflows at specified times/intervals
- Cron expression-based scheduling
- Business use cases: daily reports, weekly newsletters, periodic system checks
- Typical combinations: Schedule Trigger → Data Collection → Report Generation

## Typical Combination Patterns

1. **Document Processing Pipeline**: File Upload → Document Extractor → Parameter Extractor → Knowledge Retrieval → LLM Analysis → Template Transform → Answer
2. **Multi-Source Data Aggregation**: API Request (Multiple) → Variable Aggregator → Template Transform → Report Generation
3. **Intelligent Routing System**: User Input → Question Classifier → Conditional Branches → Specialized Knowledge Retrieval → Variable Aggregator → Response
4. **Batch Processing Workflow**: File List → List Operator → Iteration → Document Processing → Results Aggregation
5. **Scheduled Data Processing**: Schedule Trigger → HTTP Request (Data Collection) → Code (Processing) → Tool (Storage) → Notification
6. **Event-Driven Response System**: Webhook Trigger → Parameter Extractor → Conditional Logic → Tool (Action) → Follow-up Communication

These patterns enable sophisticated workflows for complex business requirements, from automated customer service to intelligent document processing and enterprise system integration.

## Technical Details of the General DSL Structure

### Structure of Edges
The connections between nodes are defined in the `edges` array of the graph. Each edge has the following fields:

- `id`: A unique ID for the edge
- `source`: The ID of the source node
- `target`: The ID of the target node
- `sourceHandle`: The output handle name of the source node
  - For standard nodes: `source`
  - For If-Else nodes: `true` (when the condition is met) / `false` (when it is not met) / `case1`, `case2`... (for multiple branches)
  - For Question Classifier nodes: The ID of each classification class
  - For Iteration nodes: The output after the iteration is complete
- `data`: Metadata for the edge (typically includes `sourceType` and `targetType`)

### Common Fields for Nodes
The common YAML structure for all nodes:

- `id`: A unique ID for the node (in UUID format)
- `position`: The coordinates on the canvas `{x, y}`
- `data`: Node-specific configuration data
  - `title`: Display name
  - `type`: Node type (e.g., `start`, `llm`, `code`, `http-request`, `if-else`)
- `width`: The width of the node (in pixels)
- `height`: The height of the node (in pixels)

### Syntax for Variable References
The syntax for referencing variables between nodes:

- Basic format: `{{#nodeId.variableName#}}`
- Input variables for the `start` node: `{{#startNodeId.inputVarName#}}`
- Output from the `code` node: `{{#codeNodeId.outputVarName#}}`
- Response from an HTTP request: `{{#httpNodeId.body#}}`, `{{#httpNodeId.status_code#}}`
- Conversation variables: `{{#conversation.varName#}}`
- Environment variables: `{{#env.varName#}}`
- System variables (Chatflow): `{{#sys.query#}}`, `{{#sys.user_id#}}`, `{{#sys.conversation_id#}}`, `{{#sys.files#}}`

### Defining File Input
How to accept file uploads in a `start` node:

- `type`: `file-list` or `file`
- `allowed_file_types`: An array of allowed file types (e.g., `image`, `document`, `audio`, `video`, `custom`)
- `max_length`: The maximum number of files that can be uploaded

## DSL Definition for Conversation and Environment Variables

### Conversation Variables (conversation_variables)
Used to maintain data between turns in the chat flow (advanced-chat):

- `id`: A unique ID for the variable
- `name`: The name of the variable
- `value_type`: The type of the value (e.g., `string`, `number`, `object`, `array[string]`, `array[number]`, `array[object]`)
- `description`: A description of the variable
- `value`: The initial value (default value)

Conversation variables are written in the Variable Assigner node, and read from other nodes using the format `{{#conversation.varName#}}`.

### Environment Variables (environment_variables)
Constants used throughout the workflow/chat flow (e.g., API keys):

- `id`: A unique ID for the variable
- `name`: The name of the variable
- `value_type`: The type of the value (e.g., `string`, `number`, `secret`)
- `value`: The value (the `secret` type will be encrypted and exported)
- `description`: A description of the variable

## Common Patterns of DSL Generation Errors

Common patterns of errors that occur when generating DSL programmatically:

### Edge and Connection Related
1. **Missing `sourceHandle` specification**: `sourceHandle` is not specified or is invalid for branching edges in If-Else or Question Classifier nodes
2. **Connecting to non-existent node IDs**: The `source` or `target` in the `edges` array specifies a node ID that does not exist on the graph
3. **Duplicate node IDs**: Assigning the same ID to multiple nodes in the same graph
4. **Incorrect connection direction**: Swapping `source` and `target`

### Variable Reference Related
5. **Incorrect syntax for variable references**: Incorrect use of parentheses and hash symbols in `{{#...#}}`
6. **Referring to non-existent node IDs**: The node being referenced does not exist on the actual graph
7. **Mismatch in variable names**: The variable names in the `outputs` definition of the Code node and the `return` statement do not match

### Node-Specific Configuration Related
8. **Incomplete outputs definition for Code nodes**: The variable name and type in the `outputs` definition of the Code node do not match the actual code return value
9. **Incorrect input type for Iteration nodes**: Passing a non-Array value to an input that requires an Array type
10. **Mismatch in Content-Type**: The mismatch between the body format (JSON/Form-data) and the Content-Type header
11. **Incomplete parameter definition for Parameter Extractor**: Missing required fields such as `name`, `type`, and `description` in the `parameters` array
12. **Model specification error for LLM nodes**: Specifying an LLM model that is not installed in the `model` field (e.g., `provider` or `name`)

### Structure and Metadata Related
13. **Missing required fields**: Required fields specific to each node type are not defined
14. **Inconsistency between app modes**: Using a workflow node (End) in a chat flow (advanced-chat), or vice versa
15. **Missing dependencies**: Required plugins for Tool/Plugin nodes are not defined in the `dependencies` field

## Parallel Execution Mechanism

In Dify, parallel execution of nodes is automatically determined based on the graph's edge structure, rather than requiring explicit configuration:

- **Conditions for triggering parallel execution**: When multiple edges connect from one node to multiple target nodes, the target nodes will be executed in parallel
- **Aggregation**: The results from the parallel branches are aggregated into a single variable using a Variable Aggregator node
- **DSL Representation**: No special flags or properties are required. Simply define multiple edges from the same `source` node ID to multiple `target` nodes
- **Important Note**: It is not possible to share or reference variables between parallel branches. Each branch runs independently

## Complete Documentation Content

### 1. Variable Assigner Node (en/use-dify/nodes/variable-assigner.mdx) — CURRENT

The Variable Assigner node manages persistent data in chatflow applications by writing to **conversation variables**. Unlike regular workflow variables that reset with each execution, conversation variables persist throughout an entire chat session.

#### Conversation Variables vs Workflow Variables
- **Workflow Variables**: exist only during a single workflow execution and reset when the workflow completes
- **Conversation Variables**: persist across multiple conversation turns within the same chat session

#### Configuration
- **Variable**: Select the conversation variable to write to
- **Set Variable**: Choose the source data from upstream workflow nodes
- **Operation Mode**: Determine how to update the variable (overwrite, append, clear, etc.)

#### Operation Modes by Data Type

**String:**
- Overwrite — Replace with another string variable
- Clear — Remove the current value
- Set — Manually assign a fixed value

**Number:**
- Overwrite — Replace with another number variable
- Clear — Remove the current value
- Set — Manually assign a fixed value
- Arithmetic — Add, subtract, multiply, or divide the current value by another number

**Boolean:**
- Overwrite — Replace with another boolean variable
- Clear — Remove the current value
- Set — Manually assign a fixed value

**Object:**
- Overwrite — Replace with another object variable
- Clear — Remove the current value
- Set — Manually define the object structure and values

**Array:**
- Overwrite — Replace with another array variable of the same type
- Clear — Remove all elements from the array
- Append — Add a single element to the end of the array
- Extend — Add all elements from another array of the same type
- Remove First/Last — Remove the first or last element from the array

#### Common Implementation Patterns
1. **Smart Memory System** — Analyze user input for memorable facts, extract structured info, append to persistent memories array
2. **User Preferences Storage** — Store language settings, notification preferences using Overwrite mode
3. **Progressive Checklists** — Track completion status across multiple conversation turns using array conversation variables

---

### 2. Variable Aggregator Node (en/use-dify/nodes/variable-aggregator.mdx) — CURRENT

Combines variables from different execution paths into a single unified output. When multiple branches produce similar outputs, this node eliminates the need for duplicate downstream processing.

#### The Branching Problem
Conditional workflows create parallel execution paths where only one branch runs at a time. The Variable Aggregator acts as a merge point.

#### Type Constraints
- **Same Type Rule**: All aggregated variables must be the same data type
- **Supported Types**: String, Number, Object, Boolean, Array

#### Output Behavior
Outputs the value from whichever branch actually executed.

#### Advanced Features (v0.6.10+)
Multiple aggregation groups — aggregate multiple groups of variables simultaneously, each with its own type constraint.

---

### 3. Variable Inspector (en/use-dify/debug/variable-inspect.mdx) — CURRENT

Shows all data flowing through your workflow. Captures inputs and outputs from each node after execution.

Features:
- **Viewing**: After any node runs, output variables appear in the inspector panel at the bottom
- **Editing**: Edit most variable values; downstream nodes use edited values
- **Resetting**: Revert icon restores original value; "Reset all" clears all cached variables

---

### 4. Variables Guide (versions/3-0-x/en/user-guide/workflow/variables.mdx)

Comprehensive guide covering all variable types:

#### System Variables
All system-level variables begin with `sys.`

**Workflow system variables:**
| Variable | Type | Description |
|----------|------|-------------|
| `sys.files` [LEGACY] | Array[File] | Images uploaded by users |
| `sys.user_id` | String | Unique user identifier |
| `sys.app_id` | String | Unique app identifier |
| `sys.workflow_id` | String | Records all node information |
| `sys.workflow_run_id` | String | Runtime status and execution logs |

**Chatflow system variables (additional):**
| Variable | Type | Description |
|----------|------|-------------|
| `sys.query` | String | User input in chatting box |
| `sys.files` | Array[File] | Images uploaded by users |
| `sys.dialogue_count` | Number | Conversation turn count |
| `sys.conversation_id` | String | Unique conversation session ID |
| `sys.user_id` | String | Unique user identifier |

#### Environment Variables
- Protect sensitive information (API keys, database passwords)
- Stored in the workflow, not in code
- Supports: String, Number, Secret
- Globally referenced within most nodes
- Read-only (cannot be written to)

#### Conversation Variables (Chatflow only)
- Temporarily store info within the same Chatflow session
- Referenced across multiple conversation rounds
- Like a "memo" for the LLM

**Supported data types:**
- String, Number, Object, Boolean (in latest version; legacy had 6 types without Boolean)
- Array[string], Array[number], Array[object], Array[boolean] (latest adds Array[boolean])

**Key characteristics:**
- Globally referenced within most nodes in same Chatflow
- Writing requires the Variable Assigner node
- Read-write variables

---

### 5. Key Concepts — Variables Section (en/use-dify/getting-started/key-concepts.mdx) — CURRENT

Defines 4 variable categories:

1. **Inputs**: Specified at User Input node; set at start, cannot be updated
   - Workflow: `sys.user_id`, `sys.app_id`, `sys.workflow_id`, `sys.workflow_run_id`, `sys.timestamp`
   - Chatflow: `sys.conversation_id`, `sys.dialogue_count`, `sys.user_id`, `sys.app_id`, `sys.workflow_id`, `sys.workflow_run_id`

2. **Outputs**: Each node produces outputs referenceable in subsequent nodes; cannot be updated

3. **Environment Variables**: Store sensitive information like API keys; constants, cannot be updated

4. **Conversation Variables (Chatflow only)**: Persist over multi-turn chatflow runs in a single conversation; updated via Variable Assigner node

#### Variable Referencing
- Select from dropdown in any node's input field
- Insert variable values into text inputs by typing `/` slash

---

### 6. Variable Assigner — Detailed Scenarios (versions/3-0-x/en/user-guide/workflow/node/variable-assigner.mdx)

The most comprehensive documentation with 3 detailed scenario walkthroughs:

**Scenario 1: Smart Memory (Auto-judge & Extract)**
- Set conversation variable `memories` as `array[object]`
- Use Conditional Branching to detect new info
- Extract with LLM node
- Append to `memories` array via Variable Assigner
- Code provided for escaping string↔object:

```python
# String to Object
import json
def main(arg1: str) -> object:
    input_data = json.loads(arg1)
    memory = input_data.get("memory", {})
    return {"mem": {"facts": memory.get("facts", []), "preferences": memory.get("preferences", []), "memories": memory.get("memories", [])}}

# Object to String
import json
def main(arg1: list) -> str:
    context = arg1[0] if arg1 else {}
    memory = {"memory": context}
    json_str = json.dumps(memory, ensure_ascii=False, indent=2)
    return {"result": f"<answer>{json_str}</answer>"}
```

**Scenario 2: Language Preference**
- Conversation variable `language`
- Condition check if empty → extract from user input → write via Variable Assigner
- LLM references `language` in subsequent rounds

**Scenario 3: Checklist Tracking**
- Conversation variable `ai_checklist`
- Each round: LLM checks checklist, compares with user input, updates via Variable Assigner
- Reminds user of missing items

---

### 7. Variable Inspect — Detailed (versions/3-2-x/en/user-guide/workflow/debug-and-preview/variable-inspect.mdx)

Detailed debugging panel documentation:
- **Real-time variable monitoring**: Captures all variables after node runs
- **Edit cached variables**: Manually change values without re-running upstream nodes
- **Abnormal data tracking**: Shows actual value and type of each variable
- **Global state management**: All variables in single panel (system, environment, custom, node output)

Supported Variable Types (in inspect):
| Type | Examples |
|------|----------|
| System Variables | `sys.query`, `sys.user_id`, `sys.conversation_id` |
| Environment Variables | `OPENAI_KEY`, `SECRET` |
| Custom Variables | User-defined, uploaded files |
| Node Output Variables | `output`, `structured_output`, `usage`, `loop_variable`, `iteration_variable` |

---

### Summary: Variable Types in Dify DSL

| Variable Type | Scope | Writable | Available In | Persistence |
|--------------|-------|----------|-------------|-------------|
| System Variables (`sys.*`) | Global | No | All nodes | Per-execution |
| Environment Variables | Global | No | Most nodes | Permanent (stored in workflow) |
| User Input Variables | Global | No | All downstream nodes | Per-execution |
| Node Output Variables | Local | No | Downstream nodes only | Per-execution |
| Conversation Variables | Global (Chatflow) | Yes (via Variable Assigner) | Most nodes | Per-conversation session |

### Key DSL Fields for Variables

In the DSL YAML, variables appear in:
- `workflow.conversation_variables[]` — Defines conversation variables with `id`, `name`, `value_type`, `description`, `default_value`
- `workflow.environment_variables[]` — Defines environment variables
- Node `data.inputs` — References to variables from other nodes
- Node `data.outputs` — Output variable definitions
- Variable Assigner node: `data.inputs.assigned_variable_selector` and `data.inputs.write_mode`
- Variable Aggregator node: `data.advanced_settings.groups[].variables[]`
