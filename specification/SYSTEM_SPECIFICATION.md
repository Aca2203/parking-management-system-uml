# Parking Management System (PMS) specification

---

## 1. Introduction

### 1.1 Purpose of this document

This document contains a full structured specification of the **Parking Management System** (in the rest of the document reffered to as **PMS**).
It precisely defines the **system's purpose**, **business model**, **system operation** and **administrative functions** of the system.

### 1.2 System overview

**PMS** is a **centralized multitenant software system** used for managing parking facilities with the help of automated devices for:

* Vehicle entry control
* Vehicle exit control
* Payment processing

The system is operated by an **operator**, who provides services to multiple independent **clients**.

---

## 2. Business model

### 2.1 Operator

The **operator** is an investor responsible for:

* Providing hardware and software infrastructure
* Installing and maintaining peripheral devices
* Operating the centralized PMS
* Performing parking payment transactions

Each operator owns one centralized PMS instance that serves multiple clients.

### 2.2 Client

A **client** is an owner of one or more parking facilities.

Examples:
* Supermarket chain
* Shopping mall or shopping mall chain
* Private parking owner

Clients define:
* Parking programs
	* Parking time limit
* Price lists
	* Subscription ticket price
	* Season ticket price
* Special parking conditions

More details about parking programs, price lists and special parking conditions can be found in the [Client administrator capabilities](#61-client-administrator-capabilities) section.

### 2.3 Business contract

A **business contract** is a formal agreement between an operator and a client.

After contract signing:
* The operator installs the necessary equipment at one or multiple facilities owned by the client.
* PMS manages entry, exit and charging operations

At the end of an accounting period:
* The client issues an invoice to the operator.
* The invoice amount equals collected parking fees reduced by the contracted operator commission.

### 2.4 Parking facility

A **parking facility** is a controlled space for parking motor vehicles.

Types:
* outdoor parking
* indoor parking (garage)

Each parking facility contains:
* Multiple entrances
* Multiple exits
* Multiple payment machines
* Defined number of parking spaces

### 2.5 Parking facility user
A **parking facility user** (in the rest of the document reffered to as **user**) is a person who uses the services of a parking facility.

---

## 3. Peripheral devices

These devices are not a part of the PMS, but communicate with it using the **standard socket interface** or other methods depending on the type or version of those devices.

### 3.1 Ticket dispensing unit
A **ticket dispensing unit** (in the rest of the document referred to as **TDU**) is a device that issues parking tickets, each with a **unique internal code**.

### 3.2 License plate recognition unit
A **license plate recognition unit** (in the rest of the document referred to as **LPRU**) is a device that reads vehicle's license plate numbers.

### 3.3 Gate control unit
A **Gate control unit** (in the rest of the document referred to as **GCU**) is a device that controls opening and closing of gates.

### 3.4 Parking payment machine
A **Parking payment machine** (in the rest of the document referred to as **payment machine**) is a device which processes parking payments and communicates transaction data to PMS.

---

## 4. System operation

### 4.1 Vehicle entry

#### 4.1.1 Entrance description

Each parking facility has one or multiple entrances.

Each entrance contains: 
* gate
* TDU
* LPRU
* GCU

#### 4.1.2 Entry procedure

1. Driver approaches the ramp and presses a button on the TDU.
2. TDU sends an event message to the PMS.
3. PMS requests a license plate number from the LPRU.
4. LPRU returns a license plate number if the reading was successful or an error message if the reading was unsuccessful.
5. PMS evaluates entry conditions:
   * Is the vehicle banned?
   * Is parking allowed?
   * Is payment required?
6. If the vehicle is allowed to enter and payment is required:
   * PMS sends a message to the TDU containing a unique code that needs to be printed.
   * PMS stores:
		* ticket code
		* license plate number
		* time of entry
		* parking facility identifier
		* gate identifier
		
   For privileged vehicles or drivers that have a subcription ticket, issuing a ticket is not required.
7. PMS sends a message to the GCU to open the gate.
8. Driver takes the ticket and goes through the gate. The GCU closes the gate when it detects that the vehicle has passed it.

### 4.2 Parking fee

#### 4.2.1 Payment description

Before exiting the parking facility, user that received a ticket on the entrance has to:
* pay the parking fee, or
* get a confirmation that he did not exceed the time limit for free parking.

Parking fee payment is done on one of the payment machines in the parking facility.

#### 4.2.2 Payment procedure

1. User inserts the ticket into the payment machine.
2. Payment machine sends the ticket code to the PMS.
3. PMS replies with the value of the parking fee that needs to be paid or with the information that no payment is required.
4. Payment machine offers the option of paying the specified amount in cash or by payment card.
5. User pays the amount in one of the specified ways.
6. Payment machine prints a receipt.
7. Payment machine sends a transaction confirmation message to the PMS, along with:
   * the value of the parking fee
   * ticket code
   * the unique transaction number.

### 4.3 Vehicle exit

#### 4.3.1 Exit description

Each parking facility has one or multiple exits.

Each exit contains:
* gate
* LPRU
* GCU

#### 4.3.2 Exit procedure

1. Vehicle approaches the gate.
2. LPRU reads the license plate and sends the license plate number to the PMS if the reading was successful or an error message if the reading was unsuccessful.
3. PMS verifies whether:
   * the parking is regulated (paid or free)
   * the payment validity period hasn't expired (configured by the parking facility)
   
4. PMS sends a message to the GCU to open the gate
5. The GCU closes the gate when it detects that the vehicle has passed it.

---

### 5. Special conditions

#### 5.1 Parking capacity control

PMS tracks how many parking spaces are free for every parking facility by counting how many vehicles have entered and exited the facility.

Reserved spaces are considered occupied.

Entry into a parking facility is denied if no free parking spaces exist.

#### 5.2 Special mode

System allows in special situations, on a special command from the operator of the parking facility or in the planned time period, that the gates are kept open and parking is not charged.
All vehicles may exit the parking facility unconditionally.

#### 5.3 Emergency mode

In emergency situations, eg. alert and in need of evacuation, devices are set up to keep the gates open.
This action is executed without the involvment of the PMS, by using intermediate connections of these devices with the alert system.

---

### 6. Administrative functions

#### 6.1 Client administrator capabilities

The system gives clients the ability to define different parking programs and price lists for every parking facility they own as listed below.
Each of the options below can be avaliable or not to the client, depending on the client desires and needs and the type of the business contract between the operator and that client.
Clients with multiple parking facilities can request easy and simple configuration of these options uniformly for all their facilities (without repeating the same configuration for every facility separately).

Listed below are options avaliable to the authorized client administrator that can configure them via a special web application avaliable on the operator's website:

1. Parking calendar.
   Administrator can define a calendar according to which parking is (not) charged or is completely closed.
   
   Example: On certain days of the week (eg. on the weekend) and holidays parking is not charged.
   
2. Daily working schedule.
   Administrator can define a schedule of times during the day when parking is (not) charged or the parking facility is completely closed.
   
   Example: Parking facility is closed at night between 10PM and 6AM.
   
3. Free parking duration.
   Administrator can define the first *n* minutes when the parking is free, after which it's charged.
   
4. Parking fee calculation method.
   Administrator can define whether the parking is charged per commenced hour or per completed hour.
   
5. Priviliged users.
   Administrator can define and maintain a list of vehicle license plates for which the parking is not charged.
   
6. Banned users.
   Administrator can define which vehicles are banned from entering the parking facility.
   
7. Price lists.
   Administrator can define price lists for different parking durations and subscription tickets.
   
   Subscription tickets can be given for a certain period (eg. a week, month, three months, six months, a year) and can be with or without reserving a specific parking space.
   Also, a client that owns multiple parking facilities can issue subscription tickets individually or for all facilities collectively.

8. Subscription tickets.
   Administrator can enter data about the users with parking subscriptions who purchased tickets directly from the operator.
   
9. Financial reports.
   Administrator can obtain different necessary reviews and financial reports for a given client for all of their parking facilities or for all facilities together, for different time intervals and tariffs.
   
10. Inspection.
    Administrator can review data about every vehicle enter and exit and every payment transaction, along with fast and easy searches by time interval, parking facility and vehicle license number.
	
Operator can access all the functions above and does so for all clients and parking facilities.

#### 6.2 User capabilities

All end users (public) can access options listed below via a special web application:

1. Overview of the number of currently avaliable spaces in all parking facilities, with easy search for facilities by client or location.

2. Overview of the currently valid parking price lists and avaliable subscription tickets for all parking facilities, with easy search for facilities by client or location.

3. Sale of subcription tickets for a specific parking facility.