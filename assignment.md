# Part 1

```sql

-- Create a Table
DROP TABLE IF EXISTS MIGRANTW;

CREATE TABLE MIGRANTW (
    Enroll_ID    INTEGER      PRIMARY KEY,
    Name         TEXT         NOT NULL,
    Skill        TEXT         NOT NULL,
    Address      TEXT         NOT NULL,
    PJobloc      TEXT         NOT NULL,
    Ph_no        TEXT         NOT NULL,
    Enroll_date  TEXT         NOT NULL
);

-- Insert records into MIGRANTW table
INSERT INTO MIGRANTW (Enroll_ID, Name, Skill, Address, PJobloc, Ph_no, Enroll_date) VALUES
(1, 'John Doe', 'Electrician,TV Mechanic', '123 Main St, Bangalore, Karnataka', 'Bangalore,Chennai', '9876543210', '2024-08-01');

INSERT INTO MIGRANTW (Enroll_ID, Name, Skill, Address, PJobloc, Ph_no, Enroll_date) VALUES
(2, 'Jane Smith', 'Carpenter', '456 Elm St, Mumbai, Maharashtra', 'Mumbai,Pune', '9876543211', '2024-08-02');

INSERT INTO MIGRANTW (Enroll_ID, Name, Skill, Address, PJobloc, Ph_no, Enroll_date) VALUES
(3, 'Robert Brown', 'Security Guard', '789 Pine St, Delhi, Delhi', 'Delhi,Noida', '9876543212', '2024-08-03');

INSERT INTO MIGRANTW (Enroll_ID, Name, Skill, Address, PJobloc, Ph_no, Enroll_date) VALUES
(4, 'Emily Davis', 'Cab Driver', '101 Maple St, Chennai, Tamil Nadu', 'Chennai,Bangalore', '9876543213', '2024-08-04');

INSERT INTO MIGRANTW (Enroll_ID, Name, Skill, Address, PJobloc, Ph_no, Enroll_date) VALUES
(5, 'Michael Johnson', 'Electrician', '202 Oak St, Noida, Uttar Pradesh', 'Noida,Delhi', '9876543214', '2024-08-05');

INSERT INTO MIGRANTW (Enroll_ID, Name, Skill, Address, PJobloc, Ph_no, Enroll_date) VALUES

(6, 'Alice Wilson', 'TV Mechanic', '303 Cedar St, Kolkata, West Bengal', 'Kolkata,Mumbai', '9876543215', '2024-08-06');

INSERT INTO MIGRANTW (Enroll_ID, Name, Skill, Address, PJobloc, Ph_no, Enroll_date) VALUES
(7, 'David Lee', 'Security Guard,Cab Driver', '404 Birch St, Bangalore, Karnataka', 'Bangalore,Mumbai', '9876543216', '2024-08-07');


-- View the Table
SELECT * FROM MIGRANTW;

--Listing the name of all migrant workers along with Skill and Ph who are either Electrician or TV Mechanic or both.

SELECT DISTINCT Name, Skill, Ph_no
FROM MIGRANTW
WHERE Skill LIKE '%Electrician%' OR Skill LIKE '%TV Mechanic%';

-- Listing the name of all migrant workers who are Security Guard and preferred to work in Bangalore.
SELECT Name
FROM MIGRANTW
WHERE Skill LIKE '%Security Guard%' AND PJobloc LIKE '%Bangalore%';

-- Creating a view with Name, Skill, PJobloc ordered by Skill for all the migrants who preferred to work in Delhi or Noida.

CREATE VIEW DelhiNoidaMigrantView AS
SELECT Name, Skill, PJobloc
FROM MIGRANTW
WHERE PJobloc LIKE '%Delhi%' OR PJobloc LIKE '%Noida%'
ORDER BY Skill;'

```

## The JDBC program using prepared statements that takes Skill as input and displays the name, ph no, address, and job locations of all migrants.

```java

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

public class MigrantWorkerSearch {
    public static void main(String[] args) {
        String jdbcURL = "jdbc:sqlite:path_to_your_database.db"; // Update this path
        String skillInput = "Electrician"; // Example skill input

        try (Connection connection = DriverManager.getConnection(jdbcURL)) {
            String sql = "SELECT Name, Ph_no, Address, PJobloc FROM MIGRANTW WHERE Skill LIKE ?";
            try (PreparedStatement statement = connection.prepareStatement(sql)) {
                statement.setString(1, "%" + skillInput + "%");

                ResultSet resultSet = statement.executeQuery();
                while (resultSet.next()) {
                    String name = resultSet.getString("Name");
                    String ph_no = resultSet.getString("Ph_no");
                    String address = resultSet.getString("Address");
                    String jobLoc = resultSet.getString("PJobloc");

                    System.out.println("Name: " + name);
                    System.out.println("Phone No: " + ph_no);
                    System.out.println("Address: " + address);
                    System.out.println("Job Locations: " + jobLoc);
                    System.out.println();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

```

```sql

--Listing state-wise no. of migrants registered. (Assuming state is part of Address)

SELECT 
    substr(Address, length(Address) - instr(reverse(Address), ',') + 2) AS State,
    COUNT(*) AS NumberOfMigrants
FROM MIGRANTW
GROUP BY State;

--Create two separate indexes against skill and job location attributes.

-- Create indexes
CREATE INDEX idx_skill ON MIGRANTW(Skill);
CREATE INDEX idx_pjobloc ON MIGRANTW(PJobloc);

-- List all migrants who are Cab driver and preferred to work in MUMBAI
SELECT Name, Skill, PJobloc
FROM MIGRANTW
WHERE Skill LIKE '%Cab Driver%' AND PJobloc LIKE '%MUMBAI%';

```


# Part 2

```sql
-- Create Company_Req Table
CREATE TABLE Company_Req (
    cname TEXT,
    job_type TEXT,
    job_location TEXT,
    vacancy INTEGER,
    skill_required TEXT,
    PRIMARY KEY (cname, job_type, job_location)
);

-- Create Place_Migrant Table
CREATE TABLE Place_Migrant (
    cname TEXT,
    enroll_ID INTEGER,
    job_type TEXT,
    osalary INTEGER,
    job_location TEXT,
    place_date TEXT,
    FOREIGN KEY (cname, job_type, job_location) REFERENCES Company_Req(cname, job_type, job_location),
    FOREIGN KEY (enroll_ID) REFERENCES MIGRANTW(Enroll_ID)
);

-- Trigger

CREATE OR REPLACE FUNCTION insert_into_place_migrant()
RETURNS TRIGGER AS $$
BEGIN
   
    DECLARE
        vacancy_count INTEGER := NEW.vacancy;
    BEGIN
       
        FOR migrant IN
            SELECT Enroll_ID
            FROM MIGRANTW
            WHERE Skill LIKE '%' || NEW.skill_required || '%'
              AND PJobloc LIKE '%' || NEW.job_location || '%'
            ORDER BY Enroll_date
            LIMIT vacancy_count
        LOOP
            
            INSERT INTO Place_Migrant (cname, enroll_ID, job_type, osalary, job_location, place_date)
            VALUES (NEW.cname, migrant.Enroll_ID, NEW.job_type, 50000, NEW.job_location, CURRENT_DATE);
        END LOOP;
        RETURN NULL;
    END;
END;
$$ LANGUAGE plpgsql;

-- Creating the trigger
CREATE TRIGGER after_insert_company_req
AFTER INSERT ON Company_Req
FOR EACH ROW
EXECUTE FUNCTION insert_into_place_migrant();


-- Add the Status column to MIGRANTW table
ALTER TABLE MIGRANTW ADD Status TEXT;

-- Update the Status of all placed migrants to "P" and others to "U"
UPDATE MIGRANTW
SET Status = CASE
    WHEN Enroll_ID IN (SELECT Enroll_ID FROM Place_Migrant) THEN 'P'
    ELSE 'U'
END;

--RollBack

BEGIN TRANSACTION;

-- Insert a record into Company_Req
INSERT INTO Company_Req (cname, job_type, job_location, vacancy, skill_required) VALUES
('TechCorp', 'Developer', 'Bangalore', 5, 'Java');

-- Check if related records are inserted into Place_Migrant
SELECT * FROM Place_Migrant;

ROLLBACK;

-- Check if the related records in Place_Migrant are removed
SELECT * FROM Place_Migrant;

-- Create a View on Details of Placed Migrants

CREATE VIEW PlacedMigrantsView AS
SELECT 
    MW.Name, 
    MW.Ph_no, 
    CR.cname AS Company_Name, 
    PM.osalary, 
    PM.job_location, 
    PM.place_date
FROM Place_Migrant PM
JOIN MIGRANTW MW ON PM.enroll_ID = MW.Enroll_ID
JOIN Company_Req CR ON PM.cname = CR.cname AND PM.job_type = CR.job_type AND PM.job_location = CR.job_location;

--Display Company-Wise Migrants Count and Total Salary Invested

SELECT 
    Company_Name,
    COUNT(*) AS Migrants_Count,
    SUM(osalary) AS Total_Salary_Invested
FROM PlacedMigrantsView
GROUP BY Company_Name;

```
## JDBC Program to Insert a Record in MIGRANTW with 12-Digit Phone Number

```java



import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class InsertMigrant {
    public static void main(String[] args) {
        String jdbcURL = "jdbc:sqlite:path_to_your_database.db"; // Update this path
        String insertSQL = "INSERT INTO MIGRANTW (Enroll_ID, Name, Skill, Address, PJobloc, Ph_no, Enroll_date, Status) VALUES (?, ?, ?, ?, ?, ?, ?, ?)";

        try (Connection connection = DriverManager.getConnection(jdbcURL);
             PreparedStatement statement = connection.prepareStatement(insertSQL)) {

            // Example values
            statement.setInt(1, 8);
            statement.setString(2, "Alice Green");
            statement.setString(3, "Electrician");
            statement.setString(4, "404 Elm St, Mumbai, Maharashtra");
            statement.setString(5, "Mumbai,Delhi");
            statement.setString(6, "123456789012"); // 12-digit phone number
            statement.setString(7, "2024-08-08");
            statement.setString(8, "U");

            // Execute the insert statement
            statement.executeUpdate();

        } catch (SQLException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}


```
```sql

---Displaying Companies with Unfilled Vacancies

DECLARE
    CURSOR c_vacancy IS
        SELECT cname, job_type, job_location, (vacancy - COALESCE(COUNT(pm.enroll_ID), 0)) AS vacancies_unfilled
        FROM Company_Req cr
        LEFT JOIN Place_Migrant pm
        ON cr.cname = pm.cname AND cr.job_type = pm.job_type AND cr.job_location = pm.job_location
        GROUP BY cr.cname, cr.job_type, cr.job_location, cr.vacancy
        HAVING (vacancy - COALESCE(COUNT(pm.enroll_ID), 0)) > 0;
BEGIN
    FOR rec IN c_vacancy LOOP
        DBMS_OUTPUT.PUT_LINE('Company: ' || rec.cname || ', Unfilled Vacancies: ' || rec.vacancies_unfilled);
    END LOOP;
END;
/

```

