# Hospital Management & Login Audit System
## 1. Project Overview

This group activity implements a **Hospital Management System** and a **Login Audit & Security Alert system**.
The project demonstrates:

* **Database design**: tables for patients, doctors, login attempts, and security alerts.
* **PL/SQL packages**: bulk processing, reusable procedures, and functions.
* **Triggers**: monitoring suspicious login attempts and generating security alerts.
* **Efficient data management**: bulk inserts using `FORALL`, use of collections, and cursor-based queries.

---

## 2. Database Tables

### **Login Audit System**

| Table             | Description                                    | Columns                                                                                        |
| ----------------- | ---------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| `login_audit`     | Stores all user login attempts                 | `audit_id` (PK), `username`, `attempt_time`, `status` (SUCCESS/FAILED), `ip_address`           |
| `security_alerts` | Stores security alerts for suspicious activity | `alert_id` (PK), `username`, `failed_attempts`, `alert_time`, `alert_message`, `contact_email` |

-- Good way of storing all login attempts for tracking security issues.
-- This table helps detect suspicious users with too many failed logins.
### **Hospital Management System**

| Table      | Description                | Columns                                                        |
| ---------- | -------------------------- | -------------------------------------------------------------- |
| `patients` | Stores patient information | `patient_id` (PK), `patient_name`, `age`, `gender`, `admitted` |
| `doctors`  | Stores doctor information  | `doctor_id` (PK), `doctor_name`, `specialty`                   |

---

## 3. Triggers

### **Login Audit Trigger**

* **Purpose**: Monitors failed login attempts.
* **Logic**:
 -- Good way of storing all login attempts for tracking security issues.

  1. Tracks failed login attempts per user per day.
  2. If failed attempts exceed 2, a record is inserted into `security_alerts`.
* **Implementation**: Used a **compound trigger** to avoid mutating table errors.

-- This trigger is a good way to monitor failed logins automatically.

-- Helps send alerts when someone tries too many times.

---

## 4. PL/SQL Package – `hospital_mgmt`

### **Purpose**

Provides reusable procedures and functions for hospital management, including bulk patient operations.

### **Package Specification**

* **Collection Types**

  * `patient_rec` – represents a single patient record.
  * `patient_table` – represents a list of patients for bulk processing.
* **Procedures & Functions**

  * `bulk_load_patients` – Inserts multiple patients at once using bulk collection and `FORALL`.
  * `show_all_patients` – Returns a cursor with all patient records.
  * `count_admitted` – Returns the number of patients currently admitted.
  * `admit_patient` – Updates a patient’s status to “admitted.”

### **Package Body**

* Implements all the above procedures and functions.
* Uses **BULK processing** for efficient insertion.
* Commits changes to ensure data consistency.

---

## 5. Testing

### **Login Audit System**

1. Insert multiple login attempts for a user.
2. Confirm that after **3 failed attempts**, a row is added to `security_alerts`.
3. Check the tables using:

```sql
SELECT * FROM login_audit;
SELECT * FROM security_alerts;
```

---

### **Hospital Management System**

1. **Bulk load patients**:

```sql
DECLARE
    p_list hospital_mgmt.patient_table := hospital_mgmt.patient_table();
BEGIN
    p_list.EXTEND(3);
    p_list(1).name := 'Alice Smith'; p_list(1).age := 30; p_list(1).gender := 'F';
    p_list(2).name := 'John Doe'; p_list(2).age := 45; p_list(2).gender := 'M';
    p_list(3).name := 'Maria Lopez'; p_list(3).age := 29; p_list(3).gender := 'F';
    hospital_mgmt.bulk_load_patients(p_list);
END;
/
```

2. **Show all patients**:

```sql
VARIABLE rc REFCURSOR;
BEGIN
    :rc := hospital_mgmt.show_all_patients;
END;
/
PRINT rc;
```

3. **Admit a patient**:

```sql
BEGIN
    hospital_mgmt.admit_patient(2);
END;
/
```

4. **Count admitted patients**:

```sql
VARIABLE total NUMBER;
BEGIN
    :total := hospital_mgmt.count_admitted;
END;
/
PRINT total;
```

---

## 6. Key Concepts Demonstrated

* **PL/SQL Packages**: Organizing reusable code.
* **Bulk Processing**: Efficient insertion of multiple rows using collections and `FORALL`.
* **Triggers**: Automated monitoring of login attempts.
* **REF CURSORs**: Returning query results from functions.
* **Data Integrity**: Proper use of commits and constraints.

---

## 7. How to Run

1. Open **SQL Developer** or **SQL*Plus**.
2. Run the **table creation scripts**.
3. Create the **package specification** and **package body**.
4. Create the **login audit triggers**.
5. Use the **test scripts** above to verify functionality.

---

## 8. Expected Outcomes

* Login attempts are recorded in `login_audit`.
* Suspicious login attempts trigger entries in `security_alerts`.
* Bulk patient data can be inserted efficiently.
* Patient admission status is tracked correctly.
* All procedures and functions can be reused in other scripts or applications.

Thank you
