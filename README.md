```python
import pandas as pd
import sqlite3
import uuid
```


```python
cols_list = ["first_name","last_name","salary","dept_name","salary_increment"]
employees=pd.read_csv("flat_data.csv",usecols=cols_list)
```

## Create our pretend employee and department tables


```python
con = sqlite3.connect('pumpjack.db')
cur = con.cursor()

cur.execute('''CREATE TABLE employee
               (id uuid, first_name text, last_name text, salary numeric, department_id uuid)''')

cur.execute('''CREATE TABLE department
               (id uuid, name text, salary_increment numeric)''')

cur.execute('''CREATE TABLE updated_salaries
               (employee_id uuid, updated_salary numeric)''')

```




    <sqlite3.Cursor at 0x7fd01f57bd50>




```python
employee_uuid = 0
department_uuid = 0


for employee in employees.values:
    
    employee_uuid = uuid.uuid4()
    department_uuid = uuid.uuid4()
    employee_name = employee[0] + " " + employee[1]
    
    cur.execute(f"INSERT INTO employee VALUES ('{employee_uuid}','{employee[0]}','{employee[1]}',{employee[2]},'{department_uuid}')")
    cur.execute(f"INSERT INTO department VALUES ('{department_uuid}','{employee_name}', {employee[2] * employee[4] / 100})")

con.commit()
#
```


```python
for employee in employees.values:
    print(employee)
```

    ['Darius' 'Mufutau' 3901 'Finance' 10]
    ['Tiger' 'Elliott' 5489 'IT' 15]
    ['Malik' 'Macaulay' 5444 'Sales' 17]
    ['Ali' 'Vance' 8993 'Marketing' 16]
    ['Randall' 'Deacon' 9515 'IT' 15]
    ['Josiah' 'Lee' 8113 'Sales' 17]
    ['Dante' 'Mohammad' 8446 'Sales' 17]
    ['Wyatt' 'Kuame' 4817 'Marketing' 16]
    ['Quinn' 'Oliver' 5513 'Finance' 10]
    ['Oliver' 'Gary' 5158 'IT' 15]
    ['Thane' 'Phelan' 4957 'Sales' 17]
    ['Walter' 'Lester' 3864 'Finance' 10]
    ['Samson' 'Dolan' 6855 'IT' 15]
    ['Beck' 'Walker' 7077 'Sales' 17]
    ['Lucas' 'Marshall' 7499 'Marketing' 16]
    ['John' 'Nash' 4269 'IT' 15]
    ['Quinlan' 'Elliott' 7503 'Sales' 17]
    ['Ivan' 'Dennis' 4048 'Sales' 17]
    ['Wang' 'Ronan' 9319 'Marketing' 16]
    ['Stone' 'Jameson' 9354 'Finance' 10]
    ['Clayton' 'Jarrod' 4102 'IT' 15]
    ['Cain' 'Sean' 7353 'Sales' 17]



```python
con.commit()
con.close() # close and commit data

```

## Read from tables and calculate updated salary


```python
con = sqlite3.connect('pumpjack.db')
cur = con.cursor()
cur2 = con.cursor()
cur3 = con.cursor()

for row in cur.execute('SELECT * FROM employee'):
    
    employee_id = row[0]
    department_uuid = row[4]
    salary = row[3]
    
    department_row = cur2.execute(f"SELECT * FROM department WHERE id='{department_uuid}'")
    
    for val in department_row:
        salary_increment = val[2]
         
    cur3.execute(f"INSERT INTO updated_salaries VALUES ('{employee_id}', {salary + salary_increment})")    
```


```python
for row in cur.execute('SELECT * FROM updated_salaries ORDER BY updated_salary'):
        print(row)
```

    ('6dc1af1a-62fd-44bc-9ae4-0eabdda1e026', 4250.4)
    ('522fafcc-5356-4332-9193-0854b0df02e5', 4291.1)
    ('86580774-622d-4fde-9fbe-a935fd032bf7', 4717.3)
    ('a487805b-5411-4ef2-bc11-73f6e279e488', 4736.16)
    ('d98caff6-7250-4d1f-82da-f594073611ab', 4909.35)
    ('f62574f2-d8ad-4f39-9160-ed54b3759916', 5587.72)
    ('6f4c5a5f-94e7-4804-9448-b1403a4c4dc6', 5799.6900000000005)
    ('81b4567a-625d-457f-b51c-faef5d2eca6e', 5931.7)
    ('6f8f788f-26d9-4afa-9a5c-a82dc678f0e5', 6064.3)
    ('9a010414-9778-4b4a-b8b7-aab2589d6cb2', 6312.35)
    ('b1d59ae1-9b24-4286-81d9-66722c73e152', 6369.48)
    ('a1733f60-72af-42a5-9dd4-90f4ba995afe', 7883.25)
    ('4187b088-c137-4712-b743-cc493f8e5b9e', 8280.09)
    ('76271dd9-066c-4dc8-bdd8-1ff0fa7806a1', 8603.01)
    ('28e0f4c4-35c5-4ce2-886f-91328068ab6f', 8698.84)
    ('1865302c-48b1-422e-bd5c-b6edbc5a8d84', 8778.51)
    ('7fd9262c-4f99-46f3-80e0-3df7456b13ca', 9492.21)
    ('159836aa-ff7b-4f9b-8fa8-9937665e1704', 9881.82)
    ('d924462d-ef4b-4e50-940f-3e15e6bff67f', 10289.4)
    ('54d00e34-c43f-4518-9bde-b9985dbf47ab', 10431.880000000001)
    ('224e824f-7673-4ad1-93bd-c714991fdfbe', 10810.04)
    ('5a0230de-b78a-4b60-ba1d-0e28099183a2', 10942.25)



```python

```

