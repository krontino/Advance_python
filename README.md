# Advance_python
Smart Employee Management &amp; Payroll System
import mysql.connector
from datetime import date

# ==========================
# DATABASE CONFIG
# ==========================
DB_CONFIG = {
    "host": "localhost",
    "user": "root",
    "password": "root",
    "database": "enterprise_db"
}

def get_connection():
    return mysql.connector.connect(**DB_CONFIG)

# ==========================
# EMPLOYEE CLASS
# ==========================
class Employee:

    def add_employee(self, emp_id, name, dept_id, hire_date, salary):
        con = get_connection()
        cur = con.cursor()
        cur.execute(
            "INSERT INTO employees VALUES (%s,%s,%s,%s,%s)",
            (emp_id, name, dept_id, hire_date, salary)
        )
        con.commit()
        con.close()
        print("✅ Employee added successfully")

    def view_employees(self):
        con = get_connection()
        cur = con.cursor()
        cur.execute("""
            SELECT e.emp_id, e.emp_name, d.dept_name, e.base_salary
            FROM employees e
            JOIN departments d ON e.dept_id = d.dept_id
        """)
        for row in cur.fetchall():
            print(row)
        con.close()

# ==========================
# ATTENDANCE CLASS
# ==========================
class Attendance:

    def mark_attendance(self, emp_id, att_date, status):
        con = get_connection()
        cur = con.cursor()
        cur.execute(
            "INSERT INTO attendance VALUES (%s,%s,%s)",
            (emp_id, att_date, status)
        )
        con.commit()
        con.close()
        print("✅ Attendance marked")

# ==========================
# PAYROLL CLASS
# ==========================
class Payroll:

    def generate_payroll(self, month):
        con = get_connection()
        cur = con.cursor()
        cur.callproc("generate_payroll", [month])
        con.commit()
        con.close()
        print("✅ Payroll generated")

    def view_payroll(self):
        con = get_connection()
        cur = con.cursor()
        cur.execute("""
            SELECT e.emp_name, p.pay_month, p.net_salary
            FROM payroll p
            JOIN employees e ON p.emp_id = e.emp_id
        """)
        for row in cur.fetchall():
            print(row)
        con.close()

# ==========================
# ANALYTICS CLASS
# ==========================
class Analytics:

    def top_paid_employees(self):
        con = get_connection()
        cur = con.cursor()
        cur.execute("""
            SELECT emp_name, base_salary
            FROM employees
            ORDER BY base_salary DESC
            LIMIT 5
        """)
        for row in cur.fetchall():
            print(row)
        con.close()

    def department_performance(self):
        con = get_connection()
        cur = con.cursor()
        cur.execute("""
            SELECT d.dept_name, AVG(p.rating)
            FROM performance p
            JOIN employees e ON p.emp_id = e.emp_id
            JOIN departments d ON e.dept_id = d.dept_id
            GROUP BY d.dept_name
        """)
        for row in cur.fetchall():
            print(row)
        con.close()

# ==========================
# MAIN PROGRAM (MENU)
# ==========================
emp = Employee()
att = Attendance()
pay = Payroll()
ana = Analytics()

while True:
    print("\n====== EMPLOYEE MANAGEMENT SYSTEM ======")
    print("1. Add Employee")
    print("2. View Employees")
    print("3. Mark Attendance")
    print("4. Generate Payroll")
    print("5. View Payroll")
    print("6. Top Paid Employees")
    print("7. Department Performance")
    print("8. Exit")

    choice = input("Enter choice: ")

    try:
        if choice == "1":
            emp.add_employee(
                int(input("Emp ID: ")),
                input("Name: "),
                int(input("Dept ID: ")),
                input("Hire Date (YYYY-MM-DD): "),
                float(input("Salary: "))
            )

        elif choice == "2":
            emp.view_employees()

        elif choice == "3":
            att.mark_attendance(
                int(input("Emp ID: ")),
                input("Date (YYYY-MM-DD): "),
                input("Status (Present/Absent/Leave): ")
            )

        elif choice == "4":
            pay.generate_payroll(input("Payroll Month (YYYY-MM-DD): "))

        elif choice == "5":
            pay.view_payroll()

        elif choice == "6":
            ana.top_paid_employees()

        elif choice == "7":
            ana.department_performance()

        elif choice == "8":
            print("👋 Exiting system")
            break

        else:
            print("❌ Invalid choice")

    except Exception as e:
        print("⚠️ Erro
