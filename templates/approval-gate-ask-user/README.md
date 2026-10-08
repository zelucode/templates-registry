# Approval Gate (Ask User)

Pauses a workflow before a consequential step and asks a person to approve it. Use it as the "are you sure?" step in front of anything you cannot easily undo, such as a payment, a delete or a send.

## What happens

1. The workflow asks: "Approve payment of 4,210.00 to ACME Supplies for invoice INV-1042?" with Yes / No buttons.
2. **Approved:** a "Payment approved" notification appears. This is where your real payment step would go.
3. **Rejected:** a second prompt asks for a reason (optional; cancel to skip), then a "Payment rejected" notification shows it.

## Before you run it

- **Run it manually from the app.** The prompts need someone at the screen. Scheduled and remote runs stop at the first prompt unless **Allow Unattended** is turned on for that node.
- Each prompt times out after 120 seconds.
- Nothing is changed on your machine. It is safe to run as often as you like.

## Make it yours

- Edit the **invoiceNumber**, **amount** and **payee** variables, or feed them from earlier steps.
- Put your real action after the approved branch, and a log or message step after the rejected one.
- For a richer review screen with several fields, see the **Invoice Review Form** template.
