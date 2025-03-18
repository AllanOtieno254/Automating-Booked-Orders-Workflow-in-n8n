# Automating Booked Orders Workflow in n8n

## Overview
This project demonstrates how to automate the process of handling booked orders using **n8n**, an automation platform. The workflow includes fetching booked orders, calculating key metrics, notifying the team via Discord, and scheduling the automation.
![Main project workflow ](https://github.com/user-attachments/assets/1023cd3e-cd60-4db0-a8d0-6d1f1fa4724f)

## Table of Contents
1. [Fetching Booked Orders](#fetching-booked-orders)
2. [Calculating Booked Orders](#calculating-booked-orders)
3. [Notifying the Team](#notifying-the-team)
4. [Scheduling the Workflow](#scheduling-the-workflow)

---

## 1. Fetching Booked Orders
### Steps to Fetch Orders:
- Add an **HTTP Request node** to your workflow.
- Configure the node to fetch data from the API endpoint providing booked orders.
- Ensure authentication is set up correctly if required by the API.
- Test the request to confirm data retrieval.

---

## 2. Calculating Booked Orders
### About the Code Node
To process and calculate the booked orders, we use the **Code node**, which allows custom **JavaScript** code execution. The Code node has two modes:
1. **Run Once for All Items** – Processes all items together.
2. **Run Once for Each Item** – Processes items individually.

### Expected Data Structure
n8n structures data as an array of objects in JSON format:
```json
[
    {
       "json": {
           "orderPrice": 100,
           "customer": "John Doe"
       }
    },
    ...
]
```

### Implementing the Code Node
1. Add a **Code node** connected to the previous node.
2. Configure the following parameters:
   - **Mode:** Run Once for All Items
   - **Language:** JavaScript
3. Replace the default code with the following:
```javascript
let items = $input.all();
let totalBooked = items.length;
let bookedSum = 0;

for (let i=0; i < items.length; i++) {
  bookedSum += items[i].json.orderPrice;
}

return [{ json: { totalBooked, bookedSum } }];
```

---

## 3. Notifying the Team
### Using the Discord Node
To notify the team of the booked orders, we use the **Discord node**.

### Steps to Configure:
1. Add a **Discord node** connected to the **Code node**.
2. Select **Message Actions** → **Send a Message**.
3. Set **Connection Type** to **Webhook**.
4. Create new credentials and enter the **Webhook URL** received from Discord.
5. Configure the **Message** field using an expression:
```text
This week we've {{$json["totalBooked"]}} booked orders with a total value of {{$json["bookedSum"]}}.
```
6. Test the Discord node to ensure messages are sent correctly.

---

## 4. Scheduling the Workflow
### Replacing the Manual Trigger
To automate the workflow execution, replace the **Manual Trigger node** with the **Schedule Trigger node**.

### Steps to Configure:
1. Remove the **Manual Trigger node**.
2. Add a **Schedule Trigger node**.
3. Configure the following parameters:
   - **Trigger Interval:** Weeks
   - **Weeks Between Triggers:** 1
   - **Trigger on weekdays:** Monday
   - **Trigger at Hour:** 9 AM
   - **Trigger at Minute:** 0
4. Connect the **Schedule Trigger node** to the **HTTP Request node**.

### Important Note:
Ensure the **n8n instance timezone** is correctly configured for accurate scheduling.

---

## Conclusion
This workflow successfully automates the process of fetching, processing, and reporting booked orders using **n8n**. The scheduled execution ensures timely updates, and team notifications keep stakeholders informed. 🚀

For further improvements, consider:
- Adding error handling in the Code node.
- Enhancing notifications with order details.
- Expanding the workflow to integrate with other platforms like Slack or email.

Happy automating! 🎯

