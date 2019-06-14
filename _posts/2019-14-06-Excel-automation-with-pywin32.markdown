---
title:  "Automating Excel tasks with Pywin32"
date:   2019-06-14 20:45:16
categories: articles
---

Besides my regular job, I'm also a teaching assistant at the strategic planning course in my university. There, to evaluate the students, we give them a business scenario and an Excel file with a set of questions in a predefined template.

Image example 1

However, grading these files is really a hassle because you have to open one by one, adjust the zoom level and the size of the cells for a proper reading (even some students use LibreOffice and when you open the files in Excel the format goes all wonky). 

In order to reduce all this process of grading files, I decided that I'll use Python to extract all the information into a little report and with the help of another Excel file I'll put the grades back in each of the student's files.

### Introduction

Python has many options for working with Excel files (pandas, openpyxL, xlsxwriter, etc). However, there is another option to work directly with the functionalites of Windows OS programs called “Python for Windows Extensions” also known as pywin32. With this package, you can easily access Window’s Component Object Model (COM) and control Microsoft applications via python. 

##### What is COM?

The Microsoft Component Object Model (COM) is a platform-independent, distributed, object-oriented system for creating binary software components that can interact. COM is the foundation technology for Microsoft's OLE (compound documents), ActiveX (Internet-enabled components), as well as others that allows us to control Windows applications from another program.

With the use of this technology, pywin32 allows us to interact with COM objects and do almost anything that a Microsoft Application can do with some python code.

### Getting Started

First, be sure that you have pywin32 installed in your computer. If not, you can easilly install it using pip:

```python
pip install pywin32
``` 

### Opening a file

In order to get the data from the Excel files, we need to open them. To do this, we need to activate the application and then make it open a file in a desired path:

``` python
import win32com.client

# Open up Excel and make it visible
excel = win32com.client.Dispatch('Excel.Application')
excel.Visible = True

# Select a file and open it
file = "path_of_file"
workbook = excel.Workbooks.Open(file)

# Wait before closing it
_ = input("Press enter to close Excel")
excel.Quit()
```

Image example 2

Once we opened a file, we are able to manipulate it and get all of its data to write it into our answers report.

### Extracting the data

Then, to extract the data of the Excel file and manipulate it to save it into a more readable format, we need to access each of the cells. In this case, since I have a predefined format, I will go directly into those cells and use a .txt file to put a summary of all the worksheets I need.

```python
import win32com.client
import sys, io

# Open up Excel and make it visible (actually you don't need to make it visible)
excel = win32com.client.Dispatch('Excel.Application')
excel.Visible = True

# Redirect the stdout to a file
orig_stdout = sys.stdout
bk = io.open("Answers_Report.txt", mode="w", encoding="utf-8")
sys.stdout = bk

# Select a file and open it
file = "path_of_file"
wb_data = excel.Workbooks.Open(file)
  
# Get the answers to the Q1A and write them into the summary file
mission=wb_data.Worksheets("1ayb_MisiónyVisiónFutura").Range("C6")
vision =wb_data.Worksheets("1ayb_MisiónyVisiónFutura").Range("C7")
print("Question 1A")
print("Mission:",mission)
print("Vision:" ,vision)
print()

# Get the answers to the Q1B and write them into the summary file
oe1=wb_data.Worksheets("1ayb_MisiónyVisiónFutura").Range("C14")
ju1=wb_data.Worksheets("1ayb_MisiónyVisiónFutura").Range("D14")
oe2=wb_data.Worksheets("1ayb_MisiónyVisiónFutura").Range("C15")
ju2=wb_data.Worksheets("1ayb_MisiónyVisiónFutura").Range("D15")
print("Question 1B")
print("OEN1:",oe1, "- JUSTIF:",ju1)
print("OEN2:",oe2, "- JUSTIF:",ju2)
print()
    
# Close the file without saving
wb_data.Close(True)

# Closing Excel and restoring the stdout
sys.stdout = orig_stdout
bk.close()
excel.Quit()
```

Image example 

With this little code we are able to get all the desired data and write it into a .txt report with the answers of a student.

### Repeating the process for multiple files

Then, I need to go through all the files of each student and make the summary and the grading template. For this, I will place all the files in a folder and then repeat the previous process for each one. Also, we need to create a new file for the grading template and write the path of each student file.

```python
import win32com.client
import glob
import sys, io

# Open up Excel and make it visible (actually you don't need to make it visible)
excel = win32com.client.Dispatch('Excel.Application')
excel.Visible = True

# Select the path of the folder with all the files
files = glob.glob("folder_path/*.xlsx")

# Redirect the stdout to a file
orig_stdout = sys.stdout
bk = io.open("Answers_Report.txt", mode="w", encoding="utf-8")
sys.stdout = bk

# Go through all the files in the folder
for file in files:
	print(file.split('\\')[1])
	wb_data = excel.Workbooks.Open(file) 
  
  # Get the answers to the Q1A
	mission=wb_data.Worksheets("1ayb_MisiónyVisiónFutura").Range("C6")
	vision =wb_data.Worksheets("1ayb_MisiónyVisiónFutura").Range("C7")
	print("Question 1A")
	print("Mission:",mission)
	print("Vision:" ,vision)
	print()

  # Get the answers to the Q1B
	oe1=wb_data.Worksheets("1ayb_MisiónyVisiónFutura").Range("C14")
	ju1=wb_data.Worksheets("1ayb_MisiónyVisiónFutura").Range("D14")
	oe2=wb_data.Worksheets("1ayb_MisiónyVisiónFutura").Range("C15")
	ju2=wb_data.Worksheets("1ayb_MisiónyVisiónFutura").Range("D15")
	print("Question 1B")
	print("OEN1:",oe1, "- JUSTIF:",ju1)
	print("OEN2:",oe2, "- JUSTIF:",ju2)
	print()

  # Get the answers to the Q2A
	mision=wb_data.Worksheets("2a_MisionyVisionSI").Range("C6")
	vision=wb_data.Worksheets("2a_MisionyVisionSI").Range("C7")
	print("Question 2A")
	print("Mission SI:",mision)
	print("Vision SI:",vision)
	print()
  
  # Get the answers to the Q3A
	print("Question 3A")
	for i in range(5,13): 
		proy=wb_data.Worksheets("3a_ProySI").Range("B"+str(i))
		desc=wb_data.Worksheets("3a_ProySI").Range("D"+str(i))
		mcfr=wb_data.Worksheets("3a_ProySI").Range("E"+str(i))
		tipo=wb_data.Worksheets("3a_ProySI").Range("F"+str(i))	
		print("\tProyect:",proy)
		print("\tDesc:",desc)
		print("\tMacFarlan:",mcfr,"- Tipo",tipo)
		print()
    
  # Close the file without saving
	wb_data.Close(True)

# Restoring the stdout
sys.stdout = orig_stdout
bk.close()

# Create a new Excel file for the grading template
wb_template = excel.Workbooks.Add()

# Headers of the template
wb_template.Worksheets(1).Range("A1").Value = 'File'
wb_template.Worksheets(1).Range("B1").Value = 'Q1A'
wb_template.Worksheets(1).Range("C1").Value = 'C1A'
wb_template.Worksheets(1).Range("D1").Value = 'Q1B'
wb_template.Worksheets(1).Range("E1").Value = 'C1A'
wb_template.Worksheets(1).Range("F1").Value = 'Q2A'
wb_template.Worksheets(1).Range("G1").Value = 'C2A'
wb_template.Worksheets(1).Range("H1").Value = 'Q3A'
wb_template.Worksheets(1).Range("I1").Value = 'C3A'

# Add the path of each file into the template
for idx, arch in enumerate(files):
	wb_template.Worksheets(1).Range("A"+str(idx+2)).Value = arch.replace('\\','/')	

# Save the grading template without alerts
excel.DisplayAlerts = False
wb_template.SaveAs(r'folder_path\Grades_Template.xlsx')

# Close the file and the program
wb_template.Close()
excel.DisplayAlerts = True
excel.Quit()
```

Finally, with this I'm able to get the summary of all the files in a little .txt and a template for grading each of the students

Image example 3

### Conclusion
Whenever someone wants to work with Excel files in python, most tutorials and websites suggest the use of pandas, opepyxl or xlsxwriter packages. However, it is important to know that there are other options availabe that can help you with more functionalities. Microsoft’s COM technology is an option that can be used effectively through python to do tasks that might be too difficult to do otherwise and not limitting its use to only Excel. Hopefully this example can give you some inspiration on how to incorporate this library into your work.

### Next steps
With this example, I showed you how to open an set of Excel files with the same structure from a folder and extract all of its data into a .txt report. Also, how to create a new Excel file and add data to it to create a template to grade the different questions in each of the files.

In the next tutorial, I'll show you how to read the template file with the grades and put its data into each of the student files to avoid the work of opening each individual file and working directly in them.
