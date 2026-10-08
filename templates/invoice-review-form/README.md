# Invoice Review Form (Ask User)

Pauses on a single review form so a person can check an invoice, correct the amount, choose how to pay it and then approve or reject it. A good pattern for any "human checks the data before it goes on" step.

## What happens

1. The form shows the invoice number, payee, amount and due date as read-only rows.
2. The reviewer fills in:
   - **Approved amount** (required, starts as the invoice amount)
   - **Payment method** (required: Bank transfer, Check or Card)
   - **Urgent** (optional)
3. They choose **Approve** or **Reject**. A reason is required to reject.
4. **Approved:** a notification confirms the amount and method. Your real payment step would go here.
   **Rejected:** a notification shows the reason.

## Before you run it

- **Needs DeskStride 1.0.2 or later**, which added the form mode of Ask User.
- **Run it manually from the app.** Scheduled and remote runs stop at the form unless Allow Unattended is on.
- Fully offline. It changes nothing on your machine.

## Make it yours

- Replace the sample **invoiceNumber**, **payee**, **amount** and **dueDate** variables with values from earlier steps, for example a document extraction.
- Add, remove or rename fields in the form. Answers are available to later steps as `steps.review_invoice.values.<name>`.
- For a simple yes/no gate without a form, see the **Approval Gate (Ask User)** template.
