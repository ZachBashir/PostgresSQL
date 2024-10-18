# PostgresSQL
Step 1: Database and Table Creation

-- Create 'patients' table
CREATE TABLE patients (
    patient_id UUID PRIMARY KEY,
    first_name TEXT NOT NULL,
    last_name TEXT NOT NULL,
    date_of_birth DATE,
    gender TEXT,
    race TEXT,
    ethnicity TEXT,
    address TEXT,
    city TEXT,
    state TEXT,
    country TEXT,
    postal_code TEXT
);

-- Create 'conditions' table
CREATE TABLE conditions (
    condition_id UUID PRIMARY KEY,
    patient_id UUID REFERENCES patients(patient_id),
    start_date DATE,
    end_date DATE,
    condition_code VARCHAR(10),
    description TEXT,
    FOREIGN KEY (patient_id) REFERENCES patients(patient_id)
);

-- Create 'immunizations' table
CREATE TABLE immunizations (
    immunization_id UUID PRIMARY KEY,
    patient_id UUID REFERENCES patients(patient_id),
    immunization_date DATE,
    vaccine_code VARCHAR(10),
    vaccine_description TEXT,
    FOREIGN KEY (patient_id) REFERENCES patients(patient_id)
);


Step 2: Import/Insert Data

-- Insert sample patients
INSERT INTO patients (patient_id, first_name, last_name, date_of_birth, gender, race, ethnicity, address, city, state, country, postal_code)
VALUES
('550e8400-e29b-41d4-a716-446655440000', 'John', 'Doe', '1980-05-15', 'Male', 'White', 'Non-Hispanic', '123 Main St', 'Jackson', 'MS', 'USA', '39201'),
('550e8400-e29b-41d4-a716-446655440001', 'Jane', 'Smith', '1975-08-22', 'Female', 'Black', 'Non-Hispanic', '456 Oak St', 'Jackson', 'MS', 'USA', '39202');

-- Insert sample conditions
INSERT INTO conditions (condition_id, patient_id, start_date, end_date, condition_code, description)
VALUES
('550e8400-e29b-41d4-a716-446655440002', '550e8400-e29b-41d4-a716-446655440000', '2023-01-15', NULL, 'E11', 'Type 2 Diabetes'),
('550e8400-e29b-41d4-a716-446655440003', '550e8400-e29b-41d4-a716-446655440001', '2022-07-10', '2023-01-10', 'I10', 'Hypertension');

-- Insert sample immunizations
INSERT INTO immunizations (immunization_id, patient_id, immunization_date, vaccine_code, vaccine_description)
VALUES
('550e8400-e29b-41d4-a716-446655440004', '550e8400-e29b-41d4-a716-446655440000', '2023-01-20', 'FLU', 'Influenza Vaccine'),
('550e8400-e29b-41d4-a716-446655440005', '550e8400-e29b-41d4-a716-446655440001', '2023-09-15', 'COVID', 'COVID-19 Vaccine');




Step 3: Data Cleaning

-- Check for missing data in key columns
SELECT * FROM patients WHERE first_name IS NULL OR last_name IS NULL;
SELECT * FROM conditions WHERE condition_code IS NULL OR description IS NULL;
SELECT * FROM immunizations WHERE vaccine_code IS NULL OR vaccine_description IS NULL;

-- Remove duplicates (if any)
DELETE FROM patients
WHERE patient_id IN (
    SELECT patient_id
    FROM patients
    GROUP BY patient_id
    HAVING COUNT(*) > 1
);

Step 4: Querying the Data

SELECT p.first_name, p.last_name, c.description
FROM patients p
JOIN conditions c ON p.patient_id = c.patient_id
WHERE c.description = 'Type 2 Diabetes';



Step 5: Data Extraction

SELECT p.first_name, p.last_name, i.vaccine_description, i.immunization_date
FROM patients p
JOIN immunizations i ON p.patient_id = i.patient_id
WHERE p.patient_id = '550e8400-e29b-41d4-a716-446655440000';


COPY (SELECT * FROM patients) TO '/path/to/export/patients.csv' DELIMITER ',' CSV HEADER;
