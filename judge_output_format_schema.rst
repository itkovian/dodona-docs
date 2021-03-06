.. _judge_output_format_schema:

JSON output format schemas
==========================

.. _judge_output_format_schema_full:

*Full* output
-------------

::

    {
      "$ref": "#/definitions/feedback",
      "definitions": {
        "feedback": {
          "description": "Root node of the test hierarchy that contains all tests of the submission.",
          "type": "object",
          "required": ["accepted", "status"],
          "properties": {
            "accepted":    { "$ref": "#/definitions/accepted" },
            "status":      { "$ref": "#/definitions/status" },
            "description": { "$ref": "#/definitions/description" },
            "messages":    { "type": "array", "items": { "$ref": "#/definitions/message" } },
            "groups":      { "type": "array", "items": { "$ref": "#/definitions/tab" } },
            "annotations": { "type": "array", "items": { "$ref": "#/definitions/annotation" } }
          }
        },
        "tab": {
          "description": "Node at depth 1 in the test hierarchy that represents test cases under the same tab.",
          "type": "object",
          "required": [],
          "properties": {
            "description": { "$ref": "#/definitions/description" },
            "badgeCount":  { "$ref": "#/definitions/badgeCount" },
            "messages":    { "type": "array", "items": { "$ref": "#/definitions/message" } },
            "groups":      { "type": "array", "items": { "$ref": "#/definitions/context" } }
          }
        },
        "context": {
          "type": "object",
          "description": "Node at depth 2 in the test hierarchy whose contained test cases depend on each other.",
          "required": ["accepted"],
          "properties": {
            "accepted":    { "$ref": "#/definitions/accepted" },
            "description": { "$ref": "#/definitions/message" },
            "messages":    { "type": "array", "items": { "$ref": "#/definitions/message" } },
            "groups":      { "type": "array", "items": { "$ref": "#/definitions/testcase" } }
          }
        },
        "testcase": {
          "type": "object",
          "description": "Node at depth 3 in the test hierarchy that provides an evaluation on tests.",
          "required": ["accepted"],
          "properties": {
            "accepted":    { "$ref": "#/definitions/accepted" },
            "description": { "$ref": "#/definitions/message" },
            "tests":       { "type": "array", "items": { "$ref": "#/definitions/test" } },
            "messages":    { "type": "array", "items": { "$ref": "#/definitions/message" } }
          }
        },
        "test": {
          "type": "object",
          "description": "Node at depth 4 (leaf) in the test hierarchy, providing an output diff.",
          "required": ["accepted"],
          "properties": {
            "accepted":    { "$ref": "#/definitions/accepted" },
            "description": { "$ref": "#/definitions/message" },
            "generated":   { "type": "string" },
            "expected":    { "type": "string" },
            "messages":    { "type": "array", "items": { "$ref": "#/definitions/message" } }
          }
        },
        "message": {
          "type": ["string", "object"],
          "description": "Possibly formatted string as a message to (some) users.",
          "required": ["format", "description"],
          "properties": {
            "format":      { "$ref": "#/definitions/format" },
            "description": { "$ref": "#/definitions/description" },
            "permission":  { "$ref": "#/definitions/permission" }
          }
        },
        "annotation": {
          "type": "object",
          "description": "An annotation on the source code.",
          "required": ["row", "text", "type"],
          "properties": {
            "row":     { "$ref": "#/definitions/index" },
            "column":  { "$ref": "#/definitions/index" },
            "text":    { "type": "string" },
            "type":    { "$ref": "#/definitions/severity" },
            "rows":    { "$ref": "#/definitions/index" },
            "columns": { "$ref": "#/definitions/index" }
          }
        },
        "format": {
          "type": "string",
          "description": "Either 'plain' (or 'text'), 'html' (formatted), 'markdown' (formatted), 'code' or any programming language (highlighted)."
        },
        "permission": {
          "type": "string",
          "description": "To which level of user this message is visible.",
          "enum": [
            "student",
            "staff",
            "zeus"
          ]
        },
        "badgeCount": {
          "type": "number",
          "description": "Indicates the number of issues in this tab",
          "minimum": 0
        },
        "description": {
          "type": "string"
        },
        "status": {
          "type": "string",
          "enum": [
            "compilation error",
            "runtime error",
            "memory limit exceeded",
            "time limit exceeded",
            "wrong", "wrong answer",
            "correct", "correct answer",
            "internal error"
          ]
        },
        "accepted": {
          "type": "boolean"
        },
        "index": {
          "type": "number",
          "description": "A zero-based index.",
          "minimum": 0
        },
        "severity": {
          "type": "string",
          "enum": ["error", "warning", "info"]
        }
      }
    }

.. _judge_output_format_schema_partial:

*Partial* output
----------------

::

    {
      "$ref": "#/definitions/update",
      "definitions": {
        "update": {
          "oneOf": [
              { "$ref": "#/definitions/start-judgement" },
              { "$ref": "#/definitions/start-tab" },
              { "$ref": "#/definitions/start-context" },
              { "$ref": "#/definitions/start-testcase" },
              { "$ref": "#/definitions/start-test" },
              { "$ref": "#/definitions/append-message" },
              { "$ref": "#/definitions/annotate-code" },
              { "$ref": "#/definitions/close-test" },
              { "$ref": "#/definitions/close-testcase" },
              { "$ref": "#/definitions/close-context" },
              { "$ref": "#/definitions/close-tab" },
              { "$ref": "#/definitions/close-judgement" }
          ]
        },
        "start-judgement": {
            "type": "object",
            "description": "Start on a new judgement.",
            "required": ["command"],
            "properties": {
                "command": { "enum": ["start-judgement"] }
            }
        },
        "start-tab": {
            "type": "object",
            "description": "Start on a new tab with given title. Hidden if all contexts are accepted iff hidden.",
            "required": ["command", "title"],
            "properties": {
                "command": { "enum": ["start-tab"] },
                "title":   { "type": "string" },
                "hidden":  { "type": "boolean" }
            }
        },
        "start-context": {
            "type": "object",
            "description": "Start on a new context.",
            "required": ["command"],
            "properties": {
                "command":     { "enum": ["start-context"] },
                "description": { "$ref": "#/definitions/message" }
            }
        },
        "start-testcase": {
            "type": "object",
            "description": "Start on a new testcase with given description",
            "required": ["command", "description"],
            "properties": {
                "command":     { "enum": ["start-testcase"] },
                "description": { "$ref": "#/definitions/message" }
            }
        },
        "start-test": {
            "type": "object",
            "description": "Start on a new test with given expected answer.",
            "required": ["command", "expected"],
            "properties": {
                "command":     { "enum": ["start-test"] },
                "expected":    { "type": "string" },
                "description": { "$ref": "#/definitions/message" }
            }
        },
        "append-message": {
            "type": "object",
            "description": "Append a message to the open object.",
            "required": ["command", "message"],
            "properties": {
                "command": { "enum": ["append-message"] },
                "message": { "$ref": "#/definitions/message" }
            }
        },
        "annotate-code": {
            "type": "object",
            "description": "Annotate a piece of code.",
            "required": ["command", "row", "text"],
            "properties": {
                "command": { "enum": ["annotate-code"] },
                "row":     { "$ref": "#/definitions/index" },
                "column":  { "$ref": "#/definitions/index" },
                "text":    { "type": "string" },
                "type":    { "$ref": "#/definitions/severity" },
                "rows":    { "$ref": "#/definitions/index" },
                "columns": { "$ref": "#/definitions/index" }
            }
        },
        "close-test": {
            "type": "object",
            "description": "Close the current test. Accepted iff status is correct, but you can overwrite this.",
            "required": ["command", "status", "generated"],
            "properties": {
                "command":   { "enum": ["close-test"] },
                "generated": { "type": "string" },
                "accepted":  { "type": "boolean" },
                "status":    { "$ref": "#/definitions/status" }
            }
        },
        "close-testcase": {
            "type": "object",
            "description": "Close the current testcase. Accepted iff all tests are accepted, but you can overwrite this.",
            "required": ["command"],
            "properties": {
                "command":  { "enum": ["close-testcase"] },
                "accepted": { "type": "boolean" }
            }
        },
        "close-context": {
            "type": "object",
            "description": "Close the current context. Accepted iff all testcases are accepted, but you can overwrite this.",
            "required": ["command"],
            "properties": {
                "command":  { "enum": ["close-context"] },
                "accepted": { "type": "boolean" }
            }
        },
        "close-tab": {
            "type": "object",
            "description": "Close the current tab. Badgecount is the number of not-accepted tests, but you can overwrite this.",
            "required": ["command"],
            "properties": {
                "command":    { "enum": ["close-tab"] },
                "badgeCount": { "$ref": "#/definitions/badgeCount" }
            }
        },
        "close-judgement": {
            "type": "object",
            "description": "Close the current judgement. Accepted iff all contexts are accepted, status is the worst (highest in description) of all tests, summary is the last of all tests, but you can overwrite this.",
            "required": ["command"],
            "properties": {
                "command":  { "enum": ["close-judgement"] },
                "accepted": { "type": "boolean" },
                "status":   { "$ref": "#/definitions/status" }
            }
        },
    
        "message": {
          "type": ["string", "object"],
          "description": "Possibly formatted string as a message to (some) users.",
          "required": ["format", "description"],
          "properties": {
            "format":      { "$ref": "#/definitions/format" },
            "description": { "$ref": "#/definitions/description" },
            "permission":  { "$ref": "#/definitions/permission" }
          }
        },
        "format": {
          "type": "string",
          "description": "Either 'plain' (or 'text'), 'html' (formatted), 'markdown' (formatted), 'code' or any programming language (highlighted)."
        },
        "permission": {
          "type": "string",
          "description": "To which level of user this message is visible.",
          "enum": [
            "student",
            "staff",
            "zeus"
          ]
        },
        "badgeCount": {
          "type": "number",
          "description": "Indicates the number of issues in this tab",
          "minimum": 0
        },
        "index": {
          "type": "number",
          "description": "A zero-based index.",
          "minimum": 0
        },
        "description": {
          "type": "string"
        },
        "status": {
          "type": "object",
          "description": "Describes the outcome of the judgement.",
          "required": ["enum"],
          "properties": {
            "enum":  { "$ref": "#/definitions/status-enum" },
            "human": { "type": "string" }
          }
        },
        "status-enum": {
          "type": "string",
          "enum": [
            "internal error",
            "compilation error",
            "runtime error",
            "wrong", "wrong answer",
            "correct", "correct answer"
          ]
        },
        "severity": {
          "type": "string",
          "enum": ["error", "warning", "info"]
        }
      }
    }
