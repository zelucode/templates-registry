# n8n Orders to a Desktop App

n8n is good at the cloud. This template covers the part that has no API: a desktop app. n8n sends an order to DeskStride, DeskStride types it into the app with the real mouse and keyboard, and n8n hears back when it is done.

## What happens

1. n8n sends an order (`orderId`, `customer`, `amount`) to DeskStride's Local API.
2. This workflow creates an order file, opens it in Notepad (a stand-in for any legacy app), types the order in, saves it and closes the window.
3. When the run ends, DeskStride posts the result (status, duration, run id) to the n8n webhook that called it.
4. n8n sends a Telegram message: success or failure.

Several orders sent back to back are queued and run one at a time.

## Before you run it

- **Windows only.** The desktop steps are verified on Windows. The n8n side works anywhere.
- **Turn on the Local API** in Settings, and **LAN access** too if n8n runs in Docker (it reaches DeskStride at `host.docker.internal`). Calling it with an API key needs a Pro plan.
- **Open the installed workflow and turn on Allow Remote Trigger.** Without it the API refuses the call.
- **Add `C:\Users\Public\Documents` to the allowed folders** in Workflow Settings. The workflow writes its order file there.
- **Copy the workflow id** from the Workflows page. You will paste it into n8n.
- While it runs, DeskStride controls the real mouse and keyboard. Leave the machine alone until it finishes.

## The n8n side

In n8n, choose **Import from JSON** (or paste with Ctrl+V on the canvas) and paste the workflow below. Then:

1. In **Run DeskStride workflow**, replace `PASTE_WORKFLOW_ID_HERE` and `PASTE_API_KEY_HERE`.
2. In both Telegram nodes, pick your own Telegram credential and replace `PASTE_TELEGRAM_CHAT_ID_HERE`.
3. **Activate** the workflow so the webhook (`deskstride-done`) is live.
4. Run it from the manual trigger.

```json
{
  "name": "DeskStride demo - orders to legacy desktop app",
  "nodes": [
    {
      "parameters": {},
      "id": "a1000000-0000-4000-8000-000000000001",
      "name": "Start demo (manual)",
      "type": "n8n-nodes-base.manualTrigger",
      "typeVersion": 1,
      "position": [0, 0]
    },
    {
      "parameters": {
        "url": "https://jsonplaceholder.typicode.com/todos?_limit=3",
        "options": {}
      },
      "id": "a1000000-0000-4000-8000-000000000002",
      "name": "Fetch fake orders",
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 4.2,
      "position": [220, 0]
    },
    {
      "parameters": {
        "assignments": {
          "assignments": [
            {
              "id": "f1",
              "name": "orderId",
              "value": "={{ $json.id }}",
              "type": "number"
            },
            {
              "id": "f2",
              "name": "customer",
              "value": "=Customer {{ $json.userId }}",
              "type": "string"
            },
            {
              "id": "f3",
              "name": "amount",
              "value": "={{ $json.id * 25 }}",
              "type": "number"
            }
          ]
        },
        "options": {}
      },
      "id": "a1000000-0000-4000-8000-000000000003",
      "name": "Shape order",
      "type": "n8n-nodes-base.set",
      "typeVersion": 3.4,
      "position": [440, 0]
    },
    {
      "parameters": {
        "method": "POST",
        "url": "http://host.docker.internal:8765/v1/workflows/PASTE_WORKFLOW_ID_HERE/run",
        "sendHeaders": true,
        "headerParameters": {
          "parameters": [
            {
              "name": "Authorization",
              "value": "Bearer PASTE_API_KEY_HERE"
            }
          ]
        },
        "sendBody": true,
        "specifyBody": "json",
        "jsonBody": "={{ JSON.stringify({ inputs: { orderId: $json.orderId, customer: $json.customer, amount: $json.amount }, callbackUrl: 'http://localhost:5678/webhook/deskstride-done' }) }}",
        "options": {}
      },
      "id": "a1000000-0000-4000-8000-000000000004",
      "name": "Run DeskStride workflow",
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 4.2,
      "position": [660, 0]
    },
    {
      "parameters": {
        "content": "## 1. n8n -> DeskStride\nEach order is POSTed to the Local API. DeskStride types it into the legacy app.\n\nBefore running: paste your **workflow id** and **API key** into the 'Run DeskStride workflow' node.",
        "height": 180,
        "width": 520
      },
      "id": "a1000000-0000-4000-8000-000000000005",
      "name": "Note: outbound",
      "type": "n8n-nodes-base.stickyNote",
      "typeVersion": 1,
      "position": [0, -220]
    },
    {
      "parameters": {
        "httpMethod": "POST",
        "path": "deskstride-done",
        "options": {}
      },
      "id": "a1000000-0000-4000-8000-000000000006",
      "name": "DeskStride finished (callback)",
      "type": "n8n-nodes-base.webhook",
      "typeVersion": 2,
      "position": [0, 340],
      "webhookId": "b2000000-0000-4000-8000-000000000006"
    },
    {
      "parameters": {
        "conditions": {
          "options": {
            "caseSensitive": true,
            "leftValue": "",
            "typeValidation": "loose"
          },
          "conditions": [
            {
              "id": "c1",
              "leftValue": "={{ $json.body.status }}",
              "rightValue": "success",
              "operator": {
                "type": "string",
                "operation": "equals"
              }
            }
          ],
          "combinator": "and"
        },
        "options": {}
      },
      "id": "a1000000-0000-4000-8000-000000000007",
      "name": "Run succeeded?",
      "type": "n8n-nodes-base.if",
      "typeVersion": 2.2,
      "position": [220, 340]
    },
    {
      "parameters": {
        "chatId": "PASTE_TELEGRAM_CHAT_ID_HERE",
        "text": "=✅ Order entered in the desktop app\n{{ $json.body.workflowName }}\nStatus: {{ $json.body.status }}\nTook: {{ $json.body.duration }}\nRun: {{ $json.body.id.slice(0, 8) }}",
        "additionalFields": {
          "appendAttribution": false
        }
      },
      "id": "a1000000-0000-4000-8000-000000000008",
      "name": "Telegram: success",
      "type": "n8n-nodes-base.telegram",
      "typeVersion": 1.2,
      "position": [440, 260],
      "webhookId": "c3000000-0000-4000-8000-000000000008"
    },
    {
      "parameters": {
        "chatId": "PASTE_TELEGRAM_CHAT_ID_HERE",
        "text": "=❌ Desktop run did not succeed\n{{ $json.body.workflowName }}\nStatus: {{ $json.body.status }}\nRun: {{ $json.body.id.slice(0, 8) }}",
        "additionalFields": {
          "appendAttribution": false
        }
      },
      "id": "a1000000-0000-4000-8000-000000000009",
      "name": "Telegram: failure",
      "type": "n8n-nodes-base.telegram",
      "typeVersion": 1.2,
      "position": [440, 440],
      "webhookId": "c3000000-0000-4000-8000-000000000009"
    },
    {
      "parameters": {
        "content": "## 2. DeskStride -> n8n\nWhen the desktop run ends, DeskStride POSTs the run result here (callbackUrl).\n\n**Activate this workflow** (top-right toggle) so the production webhook URL is live.",
        "height": 180,
        "width": 520
      },
      "id": "a1000000-0000-4000-8000-000000000010",
      "name": "Note: callback",
      "type": "n8n-nodes-base.stickyNote",
      "typeVersion": 1,
      "position": [0, 140]
    }
  ],
  "connections": {
    "Start demo (manual)": {
      "main": [[{ "node": "Fetch fake orders", "type": "main", "index": 0 }]]
    },
    "Fetch fake orders": {
      "main": [[{ "node": "Shape order", "type": "main", "index": 0 }]]
    },
    "Shape order": {
      "main": [[{ "node": "Run DeskStride workflow", "type": "main", "index": 0 }]]
    },
    "DeskStride finished (callback)": {
      "main": [[{ "node": "Run succeeded?", "type": "main", "index": 0 }]]
    },
    "Run succeeded?": {
      "main": [
        [{ "node": "Telegram: success", "type": "main", "index": 0 }],
        [{ "node": "Telegram: failure", "type": "main", "index": 0 }]
      ]
    }
  },
  "settings": {
    "executionOrder": "v1"
  },
  "pinData": {}
}
```

## Using it for real

Swap the Notepad steps for the actions of your own application. The order values arrive as `{{ inputs.orderId }}`, `{{ inputs.customer }}` and `{{ inputs.amount }}`, and the result goes back to n8n without any extra step.
