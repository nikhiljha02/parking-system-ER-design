# 🚗 Comic-Con Multi-Zone Parking Management System (ER Design)

## 📌 Overview

design link 
https://dbdiagram.io/d/parking-system-69d78c2d808962968458441f

This project represents the **Entity-Relationship (ER) design** for a large-scale parking system used during events like Comic-Con India.

The system manages thousands of vehicles arriving across multiple days and supports:

* Multiple vehicle types (bike, car, SUV, cab, EV)
* Reserved parking for special categories
* Multi-zone and multi-level parking structure
* Entry/exit tracking
* Ticket generation
* Payment processing

---

## 🎯 Objectives

The system is designed to answer:

* What vehicles entered the facility?
* Which parking spot was assigned?
* Which zone/level does the spot belong to?
* Was the spot reserved?
* When did the vehicle enter and exit?
* How is availability tracked?
* How are payments handled?

---

## 🧩 Core Entities

### 🚗 Vehicle

Stores vehicle details.

* id (PK)
* numberPlate (UNIQUE)
* ownerName
* phone
* vehicleCategory (bike, car, SUV, cab)
* fuelType (petrol, diesel, EV)

---

### 👤 AccessCategory

Defines user types.

* id (PK)
* name (VIP, cosplayer, exhibitor, staff, general)

---

### 🏢 ParkingZone

Represents high-level parking areas.

* id (PK)
* zoneName

---

### 🏗️ ParkingLevel

Represents levels within a zone.

* id (PK)
* parkingZoneId (FK)
* levelNumber

---

### 🅿️ ParkingSpot

Represents individual parking slots.

* id (PK)
* slotNumber
* parkingLevelId (FK)
* vehicleCategoryAllowed
* reservedCategoryId (FK, nullable)
* isEVCharging (boolean)

> Note: If `reservedCategoryId` is NULL, the spot is considered **general (unreserved)**.

---

### 🧾 ParkingSession (Core Entity)

Tracks each parking event.

* id (PK)
* vehicleId (FK)
* spotId (FK)
* accessCategoryId (FK)
* entryTime
* exitTime
* status (active, completed)

---

### 🎟️ ParkingTicket

Generated when a vehicle enters.

* id (PK)
* sessionId (FK, one-to-one)
* ticketNumber (UNIQUE)
* issuedAt

---

### 💳 Payment

Handles billing.

* id (PK)
* sessionId (FK, one-to-one)
* finalPrice
* paymentMode (cash, UPI, card)
* paymentStatus (done, processing, failed)
* paidAt

---

## 🔗 Relationships

| Relationship                    | Type             |
| ------------------------------- | ---------------- |
| Vehicle → ParkingSession        | 1 : N            |
| ParkingSpot → ParkingSession    | 1 : N            |
| AccessCategory → ParkingSession | 1 : N            |
| ParkingZone → ParkingLevel      | 1 : N            |
| ParkingLevel → ParkingSpot      | 1 : N            |
| ParkingSession → ParkingTicket  | 1 : 1            |
| ParkingSession → Payment        | 1 : 1            |
| AccessCategory → ParkingSpot    | 1 : N (optional) |

---

## 🧠 Key Design Decisions

### ✅ Parking Availability

Availability is **derived**, not stored.

A spot is available if:

* It is NOT part of any active session

---

### ✅ Reserved Parking

* VIP, Staff, Exhibitors, etc. have reserved spots
* General visitors use unreserved spots (`reservedCategoryId = NULL`)

---

### ✅ Session-Based Design

ParkingSession is the **core entity**:

* Tracks entry and exit
* Links vehicle, spot, and category
* Used for billing

---

### ✅ Ticket vs Session

* Ticket is just an identifier
* Session holds actual parking logic

---

### ✅ Payment Handling

* Payment is linked to ParkingSession
* One session → one payment

---

## 🔄 System Flow

1. Vehicle enters

   * ParkingSession created
   * ParkingTicket generated

2. Spot assigned

   * Based on vehicle type and access category

3. Vehicle exits

   * Exit time recorded
   * Fee calculated

4. Payment processed

   * Payment record created

---

## 📊 ER Diagram (Conceptual)

```
Vehicle ────────< ParkingSession >──────── ParkingSpot
                     │                        │
                     │                        ▼
                     ▼                 ParkingLevel
              ParkingTicket                │
                     │                     ▼
                     ▼               ParkingZone
                  Payment

ParkingSession ─────> AccessCategory
ParkingSpot ───────> AccessCategory (reserved)
```

---

## ✅ Features Supported

* Multiple visits per vehicle
* Reusable parking spots
* Reserved and general parking
* Real-time availability tracking
* Multi-zone, multi-level structure
* Payment tracking per session
* EV charging support

---

## 🚀 Future Enhancements

* Dynamic pricing (peak hours)
* Real-time slot tracking dashboard
* Mobile app integration
* QR-based ticket system
* Automated entry/exit gates

---

## 📌 Conclusion

This ER design provides a **scalable, normalized, and real-world applicable** solution for managing large event parking systems efficiently.

---
