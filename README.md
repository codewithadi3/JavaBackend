# JavaBackend
This project consists of the JEE code, with an interaction with the MySQL data base.

homeServlet.java
package com.tap.controllers;

import java.io.IOException;
import java.util.List;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import com.tap.daoimpl.Restarauntdaoimpl;
import com.tap.model.Restaraunt;
import com.tap.modeldao.Restarauntdao;

@WebServlet("/homeServlet")
public class homeServlet extends HttpServlet {

    private static final long serialVersionUID = 1L;

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // Fetch restaurant list from DAO
        Restarauntdao resDao = new Restarauntdaoimpl();
        List<Restaraunt> reslist = resDao.getAllres();

        // Store restaurant list in session (or request)
        HttpSession session = req.getSession();
        session.setAttribute("reslist", reslist);

        // Forward to JSP to render the page
        req.getRequestDispatcher("home.jsp").forward(req, resp);
    }
}
home.jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<%@ page import="com.tap.model.User,com.tap.model.Restaraunt" %>
<%@ page import = "java.util.*" %>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Home Page</title>
    <style>
      /* Navbar styles */
      .navbar {
        display: flex;
        align-items: center;
        padding: 10px 20px;
        background-color: #2c3e50;
        font-family: Arial, sans-serif;
      }
      .navbar-left {
        display: flex;
        align-items: center;
        flex: 0 0 auto;
      }
      .navbar-center {
        flex: 1;
        text-align: center;
      }
      .company-name {
        color: #ecf0f1;
        font-size: 24px;
        font-weight: bold;
        text-transform: uppercase;
        letter-spacing: 2px;
        white-space: nowrap;
        margin: 0;
      }
      .navbar-right {
        display: flex;
        align-items: center;
        gap: 10px;
        flex: 0 0 auto;
        position: relative;
      }
      .navbar-logo {
        height: 40px;
        width: 40px;
        margin-right: 10px;
        object-fit: contain;
      }
      .navbar-right button {
        background-color: #e67e22;
        border: none;
        color: white;
        padding: 8px 15px;
        border-radius: 4px;
        cursor: pointer;
        font-size: 16px;
        white-space: nowrap;
        transition: background-color 0.3s ease;
      }
      .navbar-right button:hover {
        background-color: #d35400;
      }
      .navbar-right button:focus {
        outline: none;
      }
      .dropdown-content {
        display: none;
        position: absolute;
        top: 100%;
        right: 0;
        background-color: #34495e;
        min-width: 160px;
        box-shadow: 0px 8px 16px rgba(0,0,0,0.2);
        border-radius: 4px;
        z-index: 1000;
      }
      .dropdown:hover .dropdown-content {
        display: block;
      }
      .dropdown-content a {
        color: #ecf0f1;
        padding: 12px 16px;
        text-decoration: none;
        display: block;
        font-size: 16px;
        white-space: nowrap;
      }
      .dropdown-content a:hover {
        background-color: #3d566e;
      }

      /* Container for all restaurant cards: flex with wrap */
      .restaurant-container {
        display: flex;
        flex-wrap: wrap;
        justify-content: center; /* center cards horizontally */
        gap: 20px;
        padding: 20px 10px;
        background-color: #f9f9f9;
      }

      /* Individual restaurant card */
      .restaurant-card {
        border: 1px solid #ddd;
        border-radius: 10px;
        padding: 15px;
        width: 280px; /* fixed width for each card */
        box-shadow: 0 4px 8px rgba(0,0,0,0.1);
        background-color: #fff;
        font-family: Arial, sans-serif;
        display: flex;
        flex-direction: column;
        align-items: center;
        transition: transform 0.2s ease;
      }
      .restaurant-card:hover {
        transform: translateY(-5px);
        box-shadow: 0 8px 16px rgba(0,0,0,0.2);
      }
      .restaurant-image {
        width: 100%;
        height: 180px;
        border-radius: 10px;
        object-fit: cover;
        margin-bottom: 15px;
      }
      .restaurant-details h3 {
        margin: 0 0 10px 0;
        color: #2c3e50;
        text-align: center;
      }
      .restaurant-details p {
        margin: 5px 0;
        color: #555;
        text-align: center;
      }

      /* Responsive: smaller screens */
      @media (max-width: 600px) {
        .restaurant-card {
          width: 90%;
        }
      }
    </style>
</head>
<body>
<%
    User u = (User) session.getAttribute("user");
    if (u != null) {
%>
    <nav class="navbar">
        <div class="navbar-left">
            <img src="images/logo.jfif" alt="Company Logo" class="navbar-logo" />
        </div>
        <div class="navbar-center">
            <div class="company-name">foods</div>
        </div>
        <div class="navbar-right">
            <div class="greeting">Hi <%= u.getUsername() %></div>
            <div class="dropdown" id="settingsDropdown">
                <button aria-haspopup="true" aria-expanded="false">Settings</button>
                <div class="dropdown-content" aria-label="submenu">
                    <a href="changePassword.jsp">Change Password</a>
                    <a href="logout.jsp">Log Out</a>
                </div>
            </div>
        </div>
    </nav>
<%
    } else {
%>
    <nav class="navbar">
        <div class="navbar-left">
            <img src="images/logo.jfif" alt="Company Logo" class="navbar-logo" />
        </div>
        <div class="navbar-center">
            <div class="company-name">foods</div>
        </div>
        <div class="navbar-right">
            <button onclick="location.href='login.html'">Login</button>
            <button onclick="location.href='register.jsp'">Register</button>
        </div>
    </nav>
<%
    }
%>

<div class="restaurant-container">
<%
    List<Restaraunt> reslist = (List<Restaraunt>) session.getAttribute("reslist");
    if (reslist != null && !reslist.isEmpty()) {
        for (Restaraunt res : reslist) {
            // Replace with actual image path or dynamic property
            String imagePath = "images/logo.jfif";
            // Example if you have image filename stored in res object:
            // String imagePath = "images/" + res.getImageFilename();
%>
    <div class="restaurant-card">
        <img src="<%= imagePath %>" alt="Image of <%= res.getRestaraunt_name() %>" class="restaurant-image" />
        <div class="restaurant-details">
            <h3><%= res.getRestaraunt_name() %></h3>
            <p><strong>Address:</strong> <%= res.getRestaraunt_address() %></p>
            <p><strong>Status:</strong> <%= res.isRestaraunt_active() ? "Active" : "Inactive" %></p>
        </div>
    </div>
<%
        }
    } else {
%>
    <p style="text-align:center; width:100%;">No restaurants found.</p>
<%
    }
%>
</div>

</body>
</html>

register.jsp
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Registration Form</title>
<style>
  /* Reset and base styles */
  * {
    box-sizing: border-box;
  }
  body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: #e9f0f7;
    margin: 0;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  form {
    background: #fff;
    padding: 30px 40px;
    border-radius: 12px;
    box-shadow: 0 8px 25px rgba(0,0,0,0.1);
    width: 360px;
  }
  h2 {
    margin-bottom: 25px;
    color: #2c3e50;
    text-align: center;
  }
  label {
    display: block;
    margin-bottom: 6px;
    font-weight: 600;
    color: #34495e;
  }
  input[type="text"],
  input[type="email"],
  input[type="password"],
  textarea {
    width: 100%;
    padding: 10px 12px;
    margin-bottom: 20px;
    border: 1.8px solid #ccc;
    border-radius: 6px;
    font-size: 16px;
    resize: vertical;
    transition: border-color 0.3s ease;
  }
  input[type="text"]:focus,
  input[type="email"]:focus,
  input[type="password"]:focus,
  textarea:focus {
    border-color: #2980b9;
    outline: none;
  }
  textarea {
    min-height: 80px;
  }
  input[type="submit"] {
    width: 100%;
    background-color: #2980b9;
    color: white;
    padding: 14px 0;
    border: none;
    border-radius: 8px;
    font-size: 18px;
    font-weight: 700;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }
  input[type="submit"]:hover {
    background-color: #1c5980;
  }
  /* Responsive */
  @media (max-width: 400px) {
    form {
      width: 90%;
      padding: 20px;
    }
  }
</style>
</head>
<body>

<form action="register" method="post">
  <h2>Create Account</h2>

  <label for="username">Username</label>
  <input type="text" id="username" name="username" placeholder="Enter your username" required />

  <label for="email">Email</label>
  <input type="email" id="email" name="email" placeholder="Enter your email" required />

  <label for="password">Password</label>
  <input type="password" id="password" name="password" placeholder="Enter your password" required />

  <label for="address">Address</label>
  <textarea id="address" name="address" placeholder="Enter your address" required></textarea>

  <input type="submit" value="Register" />
</form>

</body>
</html>

loginServlet.java
package com.tap.controllers;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import com.tap.daoimpl.Restarauntdaoimpl;
import com.tap.daoimpl.userdaoimpl;
import com.tap.model.User;

/**
 * Servlet implementation class loginServlet
 */
@WebServlet("/login")
public class loginServlet extends HttpServlet {
private HttpSession session;

@SuppressWarnings("unused")
@Override

	protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		// TODO Auto-generated method stub
	String email = req.getParameter("email");
	String password = req.getParameter("password");
	userdaoimpl user =new userdaoimpl();
	User u = user.getUserByEmail(email);
	session = req.getSession();
	session.setAttribute("user",u);
	if(password.equals(u.getPassword())) {
		if(u!=null) {
		 resp.sendRedirect("homeServlet");
	}
		else {
		resp.sendRedirect("register.jsp");
		}
	}
	}
}

login.html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Login</title>
<style>
  /* Basic reset */
  * {
    box-sizing: border-box;
  }

  body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: #f0f4f8;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    margin: 0;
  }

  form {
    background: white;
    padding: 30px 40px;
    border-radius: 12px;
    box-shadow: 0 8px 20px rgba(0,0,0,0.1);
    width: 320px;
  }

  form label {
    display: block;
    margin-bottom: 6px;
    font-weight: 600;
    color: #333;
  }

  input[type="text"],
  input[type="password"] {
    width: 100%;
    padding: 10px 12px;
    margin-bottom: 20px;
    border: 1.8px solid #ccc;
    border-radius: 6px;
    font-size: 16px;
    transition: border-color 0.3s ease;
  }

  input[type="text"]:focus,
  input[type="password"]:focus {
    border-color: #007BFF;
    outline: none;
  }

  input[type="submit"] {
    width: 100%;
    background-color: #007BFF;
    color: white;
    padding: 12px 0;
    border: none;
    border-radius: 8px;
    font-size: 18px;
    font-weight: 600;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }

  input[type="submit"]:hover {
    background-color: #0056b3;
  }

  /* Responsive */
  @media (max-width: 400px) {
    form {
      width: 90%;
      padding: 20px;
    }
  }
</style>
</head>
<body>
<form action="login">
  <label for="email">Email</label>
  <input type="text" id="email" name="email" placeholder="Enter your email" required>

  <label for="password">Password</label>
  <input type="password" id="password" name="password" placeholder="Enter your password" required>

  <input type="submit" name="submit" value="Login">
</form>
</body>
</html>


the below code for the completely new project for the task employee, create a new dynamic web project and create all the files accordingly.

allemployees.html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>view all the employees</title>
</head>
<body>
<p>click here to view all the employee deatails</p>
<a href="ShowEmployeeServlet">view all the employees</a>
</body>
</html>

ShowEmployeeServlet.java(servlet file)
package Employeeinfo;
import java.io.IOException;
import java.sql.SQLException;
import java.util.List;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import Employeeinfo.Employee.Employee;
import Employeeinfo.Employee.employeedao;

/**
 * Servlet implementation class ShowEmployeeServlet
 */
@WebServlet("/ShowEmployeeServlet")
public class ShowEmployeeServlet extends HttpServlet {
	private employeedao employeeDAO = new employeedao();

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        try {
            // Get all employees from the database
            List<Employee> employeeList = employeeDAO.getAllEmployees();

            // Store the list of employees in session
            HttpSession session = req.getSession();
            session.setAttribute("employees", employeeList);

            // Redirect to the JSP page to display employees
            resp.sendRedirect("employees.jsp");
        } catch (SQLException e) {
            e.printStackTrace();
            resp.sendError(HttpServletResponse.SC_INTERNAL_SERVER_ERROR, "Database error");
        }
    }

}

employees.jsp(jsp file)
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<%@ page import="java.util.List" %>
<%@ page import="Employeeinfo.Employee.Employee" %>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Employee Details</title>
    <style>
        /* Global Styles */
        body {
            font-family: 'Arial', sans-serif;
            background-color: #f4f4f9;
            margin: 0;
            padding: 0;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        h1 {
            text-align: center;
            color: #333;
            font-size: 2.5em;
            margin-top: 20px;
            font-family: 'Roboto', sans-serif;
        }

        /* Employee Card Container */
        .employee-container {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 20px;
            padding: 20px;
        }

        /* Employee Card Design */
        .employee-card {
            background-color: #fff;
            border: 1px solid #ddd;
            border-radius: 8px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            padding: 20px;
            width: 250px;
            height: auto;
            text-align: center;
            transition: transform 0.3s ease;
        }

        .employee-card:hover {
            transform: scale(1.05);
        }

        .employee-card p {
            font-size: 1.1em;
            margin: 10px 0;
        }

        .employee-card h3 {
            font-size: 1.5em;
            color: #333;
        }

        /* Styling for the Add Employee button */
        .add-employee-btn-container {
            display: flex;
            justify-content: center;
            margin-top: 20px;
            margin-bottom: 10px;
        }

        .add-employee-btn {
            padding: 12px 24px;
            font-size: 1.1em;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 6px;
            cursor: pointer;
            transition: background-color 0.3s ease, transform 0.2s ease;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            text-decoration: none;
        }

        .add-employee-btn:hover {
            background-color: #45a049;
            transform: scale(1.05);
        }

        /* Styling for the Edit button */
        .edit-btn-container {
            margin-top: 15px;
        }

        .edit-btn {
            padding: 10px 20px;
            font-size: 1em;
            background-color: #ffa500;
            color: white;
            border: none;
            border-radius: 6px;
            cursor: pointer;
            transition: background-color 0.3s ease, transform 0.2s ease;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            text-decoration: none;
        }

        .edit-btn:hover {
            background-color: #ff8c00;
            transform: scale(1.05);
        }

    </style>
</head>
<body>

<h1>Employee Details</h1>

<!-- Add Employee Button -->
<div class="add-employee-btn-container">
    <a href="AddEmployee.jsp" class="add-employee-btn">Add Employee</a>
</div>

<!-- Employee Cards -->
<div class="employee-container">
<%
    // Retrieve the list of employees from the session
    List<Employee> employees = (List<Employee>)session.getAttribute("employees");

    if (employees != null) {
        // Iterate through the list of employees
        for (Employee emp : employees) {
%>
    <div class="employee-card">
        <h3 class="employee-name"><%= emp.getEmp_name() %></h3>
        <p class="employee-designation">Designation: <%= emp.getEmp_desig() %></p>
        <p class="employee-salary">Salary: $<%= emp.getEmp_salary() %></p>

        <!-- Edit Button -->
        <div class="edit-btn-container">
            <a href="#" class="edit-btn">Edit</a>
        </div>
    </div>
<%
        }
    } else {
%>
        <p>No employee data available.</p>
<%
    }
%>
</div>

</body>
</html>


AddEmployee.java(servlet file)
package com.tap.controller;

import java.io.IOException;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.Statement;

import javax.servlet.ServletConfig;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

/**
 * Servlet implementation class AddEmployee
 */
@WebServlet("/AdditionEmployee")
public class AddEmployee extends HttpServlet {
	private String INSERT_EMPLOYEES = "insert into insertemp(emp_name,emp_desig,emp_salary,email) values(?,?,?,?)";
	private Connection con;

	private ResultSet res;
	private HttpSession session;
	private PreparedStatement pstmt;
	
	@Override
		public void init(ServletConfig config) throws ServletException {
			// TODO Auto-generated method stub
			try {
				Class.forName("com.mysql.cj.jdbc.Driver");
		    	con = DriverManager.getConnection("jdbc:mysql://localhost:3306/employee","root","root");
			}
			catch(Exception e) {
				e.printStackTrace();
			}
		}
	@Override
	protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		
		try {
			pstmt = con.prepareStatement(INSERT_EMPLOYEES);
			pstmt.setString(1,req.getParameter("emp_name"));
			pstmt.setString(2,req.getParameter("emp_desig"));
			pstmt.setInt(3,Integer.parseInt(req.getParameter("emp_salary")));
			pstmt.setString(4,req.getParameter("emp_email"));
			int x = pstmt.executeUpdate();
			if(x!=0) {
				resp.sendRedirect("ShowEmployeeServlet");
			}
			else {
				resp.sendRedirect("failure.jsp");
			}
			
			
		}
		catch(Exception e) {
			e.printStackTrace();
		}
	}
 

}

AddEmployee.jsp(JSP file)
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Adding a New Employee</title>

    <style>
        /* Global Styles */
        body {
            font-family: 'Arial', sans-serif;
            background-color: #f3f4f9;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 0;
        }

        h1 {
            text-align: center;
            color: #333;
            font-size: 2em;
            margin-bottom: 20px;
            font-family: 'Roboto', sans-serif;
        }

        /* Form Styles */
        .form-container {
            background: #ffffff;
            padding: 40px 60px;
            border-radius: 8px;
            box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
            max-width: 400px;
            width: 100%;
        }

        form {
            display: flex;
            flex-direction: column;
            gap: 15px;
        }

        label {
            font-size: 1.1em;
            color: #333;
        }

        input[type="text"] {
            padding: 12px;
            font-size: 1em;
            border-radius: 6px;
            border: 2px solid #ddd;
            margin-top: 8px;
            transition: all 0.3s ease;
        }

        input[type="text"]:focus {
            border-color: #4CAF50;
            outline: none;
            box-shadow: 0 0 8px rgba(72, 183, 72, 0.5);
        }

        /* Submit Button Styles */
        input[type="submit"] {
            background-color: #4CAF50;
            color: white;
            padding: 14px;
            border: none;
            border-radius: 6px;
            font-size: 1.1em;
            cursor: pointer;
            transition: all 0.3s ease;
            margin-top: 10px;
        }

        input[type="submit"]:hover {
            background-color: #45a049;
            transform: scale(1.05);
        }

        /* Input Placeholder Styles */
        input::placeholder {
            color: #888;
            font-style: italic;
        }

        /* Responsive Design */
        @media (max-width: 768px) {
            .form-container {
                padding: 30px;
                width: 100%;
            }

            h1 {
                font-size: 1.6em;
            }
        }
    </style>
</head>
<body>

<div class="form-container">
    <h1>Adding a New Employee</h1>
    <form action="AdditionEmployee" method="post">
        <label for="emp_name">Employee Name</label>
        <input type="text" id="emp_name" name="emp_name" placeholder="Enter employee name" required><br>

        <label for="emp_desig">Employee Designation</label>
        <input type="text" id="emp_desig" name="emp_desig" placeholder="Enter employee designation" required><br>

        <label for="emp_salary">Employee Salary</label>
        <input type="text" id="emp_salary" name="emp_salary" placeholder="Enter employee salary" required><br>

        <label for="emp_email">Employee Email</label>
        <input type="text" id="emp_email" name="emp_email" placeholder="Enter employee email" required><br>

        <input type="submit" value="Add Employee">
    </form>
</div>

</body>
</html>

employeedao.java
package Employeeinfo.Employee;
import Employeeinfo.*;
import java.sql.*;
import java.util.ArrayList;
import java.util.List;
public class employeedao {

    private static final String URL = "jdbc:mysql://localhost:3306/employee";
    private static final String USER = "root";
    private static final String PASSWORD = "root";

    public List<Employee> getAllEmployees() throws SQLException {
        List<Employee> employees = new ArrayList<>();
        String query = "SELECT * FROM insertemp"; // Adjust the table name accordingly

        try {
        	Class.forName("com.mysql.cj.jdbc.Driver");
        	Connection con = DriverManager.getConnection(URL, USER, PASSWORD);
            Statement stmt = con.createStatement();
            ResultSet rs = stmt.executeQuery(query);

            while (rs.next()) {
                String empName = rs.getString("emp_name");
                String empDesig = rs.getString("emp_desig");
                int empSalary = rs.getInt("emp_salary");
                
                Employee employee = new Employee(empName, empDesig, empSalary);
                employees.add(employee);
            }
 
    }
        catch(Exception e) {
        	e.printStackTrace();
        }
		return employees;
    }
}

employee.java
package Employeeinfo.Employee;
public class Employee {
	  private String emp_id;
	    private String emp_name;
	    private String emp_desig;
	    private int emp_salary;
	    private String email;
	    private int password;
		public String getEmp_id() {
			return emp_id;
		}
		public void setEmp_id(String emp_id) {
			this.emp_id = emp_id;
		}
		public String getEmp_name() {
			return emp_name;
		}
		public void setEmp_name(String emp_name) {
			this.emp_name = emp_name;
		}
		public String getEmp_desig() {
			return emp_desig;
		}
		public void setEmp_desig(String emp_desig) {
			this.emp_desig = emp_desig;
		}
		public int getEmp_salary() {
			return emp_salary;
		}
		public Employee(String emp_name, String emp_desig, int emp_salary) {
			super();
			this.emp_name = emp_name;
			this.emp_desig = emp_desig;
			this.emp_salary = emp_salary;
		}
		public void setEmp_salary(int emp_salary) {
			this.emp_salary = emp_salary;
		}
		public String getEmail() {
			return email;
		}
		@Override
		public String toString() {
			return  emp_id + "   "+emp_name +  emp_desig
					+ emp_salary +  "   " + email +  "   "+ password ;
		}
		public void setEmail(String email) {
			this.email = email;
		}
		public int getPassword() {
			return password;
		}
		public void setPassword(int password) {
			this.password = password;
		}
		public Employee(String emp_id, String emp_name, String emp_desig, int emp_salary, String email, int password) {
			super();
			this.emp_id = emp_id;
			this.emp_name = emp_name;
			this.emp_desig = emp_desig;
			this.emp_salary = emp_salary;
			this.email = email;
			this.password = password;
		}
		public Employee(String emp_name, String emp_desig, int emp_salary, String email, int password) {
			super();
			this.emp_name = emp_name;
			this.emp_desig = emp_desig;
			this.emp_salary = emp_salary;
			this.email = email;
			this.password = password;
		}
		public Employee(String emp_name, String emp_desig, int emp_salary, String email) {
			super();
			this.emp_name = emp_name;
			this.emp_desig = emp_desig;
			this.emp_salary = emp_salary;
			this.email = email;
		}
}



