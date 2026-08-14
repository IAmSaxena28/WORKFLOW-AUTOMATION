# BPMN Modeling Assignment

This repository contains three BPMN models designed using Camunda Modeler, utilizing standard building blocks: Start Events, Tasks, Exclusive Gateways, and End Events.

## Scenario 1: Employee Leave Approval
**File:** `Scenario_1_Leave_Approval.bpmn`
**Description:** This process models an HR leave request. It uses an exclusive gateway to first check if the employee has sufficient balance. If true, it moves to manager review. A second exclusive gateway routes the process to either an approval loop (updating balance and notifying) or a rejection notification. 

## Scenario 2: Online Purchase Order Processing
**File:** `Scenario_2_Purchase_Order.bpmn`
**Description:** This model tracks an e-commerce order. The system first acts as a gateway to verify stock. If in stock, payment is attempted. A second gateway evaluates the payment status—routing to a failure notification if declined, or proceeding to a sequential fulfillment path (prepare, ship, confirm) if approved.

## Scenario 3: IT Service Request
**File:** `Scenario_3_IT_Support.bpmn`
**Description:** This model manages IT ticketing. After registration and severity checking, an exclusive gateway routes the ticket to either a standard or senior technician. After investigation, a final gateway determines if the issue can be fixed internally or requires external escalation before notifying the employee.