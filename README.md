# 🧹 Data Cleaning in SQL | Data Analyst Portfolio Project

Welcome to my Data Analyst Portfolio Project! This project demonstrates **data cleaning techniques using SQL** on a real-world housing dataset from Nashville. The goal is to prepare the data for analysis by cleaning, transforming, and standardizing it using SQL Server.

---

## 🗃️ Dataset

The dataset used in this project is the **Nashville Housing dataset**, which includes property sales data with multiple inconsistencies that needed to be cleaned before analysis.

---

## 🛠️ Tools & Technologies

- **SQL Server (T-SQL)**
- Microsoft SQL Server Management Studio (SSMS)

---

## 🧽 Cleaning Steps

### 1. Standardize Date Format

Converted `SaleDate` from `datetime` to `date` type for consistency.

```sql
ALTER TABLE NashvilleHousing ADD SaleDateConverted DATE;
UPDATE NashvilleHousing SET SaleDateConverted = CONVERT(Date, SaleDate);
```

---

### 2. Populate Missing Property Addresses

Filled in missing `PropertyAddress` values by referencing records with the same `ParcelID`.

```sql
UPDATE a
SET PropertyAddress = ISNULL(a.PropertyAddress, b.PropertyAddress)
FROM NashvilleHousing a
JOIN NashvilleHousing b
  ON a.ParcelID = b.ParcelID AND a.[UniqueID ] <> b.[UniqueID ]
WHERE a.PropertyAddress IS NULL;
```

---

### 3. Split Address into Separate Columns

Extracted and stored individual components of `PropertyAddress` and `OwnerAddress`.

```sql
-- Property Address
UPDATE NashvilleHousing
SET PropertySplitAddress = SUBSTRING(PropertyAddress, 1, CHARINDEX(',', PropertyAddress) -1);

UPDATE NashvilleHousing
SET PropertySplitCity = SUBSTRING(PropertyAddress, CHARINDEX(',', PropertyAddress) + 1 , LEN(PropertyAddress));

-- Owner Address using PARSENAME
UPDATE NashvilleHousing
SET OwnerSplitAddress = PARSENAME(REPLACE(OwnerAddress, ',', '.'), 3),
    OwnerSplitCity = PARSENAME(REPLACE(OwnerAddress, ',', '.'), 2),
    OwnerSplitState = PARSENAME(REPLACE(OwnerAddress, ',', '.'), 1);
```

---

### 4. Normalize "SoldAsVacant" Field

Standardized the values in the `SoldAsVacant` column for better clarity.

```sql
UPDATE NashvilleHousing
SET SoldAsVacant = CASE 
    WHEN SoldAsVacant = 'Y' THEN 'Yes'
    WHEN SoldAsVacant = 'N' THEN 'No'
    ELSE SoldAsVacant
END;
```

---

### 5. Remove Duplicate Records

Used a Common Table Expression (CTE) and `ROW_NUMBER()` to identify and delete duplicate rows.

```sql
WITH RowNumCTE AS (
    SELECT *, ROW_NUMBER() OVER (
        PARTITION BY ParcelID, PropertyAddress, SalePrice, SaleDate, LegalReference 
        ORDER BY UniqueID
    ) row_num
    FROM NashvilleHousing
)
DELETE FROM RowNumCTE WHERE row_num > 1;
```

---

### 6. Drop Unused Columns

Removed columns that were no longer needed after cleaning and transformation.

```sql
ALTER TABLE NashvilleHousing
DROP COLUMN OwnerAddress, TaxDistrict, PropertyAddress, SaleDate;
```

---

## 📌 Key Skills Demonstrated

- Data Cleaning & Transformation
- Handling NULL values
- Removing Duplicates with CTEs
- String Manipulation in SQL
- Data Normalization

---

## 📁 Repository Structure

```
📦 Data-Cleaning-in-SQL
 ┣ 📄 README.md
 ┗ 📄 DataCleaningQueries.sql
```

---

## 📊 Future Work

This cleaned dataset is now ready for:
- Data Visualization (Power BI, Tableau)
- Exploratory Data Analysis (EDA)
- Predictive Modeling

---

## 🤝 Let's Connect

If you liked this project or have feedback, feel free to connect with me on [LinkedIn](#) or check out my other projects.
