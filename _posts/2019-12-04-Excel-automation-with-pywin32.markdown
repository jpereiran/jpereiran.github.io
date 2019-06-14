---
title:  "Automating Excel tasks with Pywin32"
date:   2019-06-12 20:45:16
categories: articles
---

Besides my regular job, I'm also a teaching assistant of the strategic planning course at my university. Here, to evaluate the students, we give them a business scenario and an Excel file with a set of questions in a predefined template.

Image example 1

However, grading these files is really a hassle because you have to open one by one, adjust the zoom level and the size of the cells for a proper reading (even some students use LibreOffice and when you open the files in Excel the format goes all wonky). 

In order to reduce all this process of grading files, I decided that I'll use Python to extract all the information into a little report and with the help of another Excel file I'll put the grades back in each of the student's files.

### Introduction

Python has many options for working with Excel files (pandas, openpyxL, xlsxwriter, etc). However, there is another option to work directly with the functionalites of Windows OS programs called “Python for Windows Extensions” also known as pywin32. With this package, you can easily access Window’s Component Object Model (COM) and control Microsoft applications via python. 

#### What is COM?

The Microsoft Component Object Model (COM) is a platform-independent, distributed, object-oriented system for creating binary software components that can interact. COM is the foundation technology for Microsoft's OLE (compound documents), ActiveX (Internet-enabled components), as well as others that allows us to control Windows applications from another program.

With the use of this technology, pywin32 allows us to interact with COM objects and do almost anything that a Microsoft Application can do with some python code.

### Getting Started

First, be sure that you have pywin32 installed in your computer. If not, you can easilly install it using pip:

{% highlight pyhon %}
pip install pywin32
{% endhighlight %}

### Opening a file

In order to get the data from the Excel files, we need to open them. To do this, we need to activate the application and then make it open a file in a desired path:

{% highlight pyhon %}
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
{% endhighlight %}

Image example 2

Once we opened our file, we are able to manipulate it and get all of its data to write it into our report with the answers of all the students.

### Extracting the data

Then, I need to extract some of the cells of the Excel file and manipulate them to save it into a more readable format. In this case, I'll use a .txt file where I will put the summary of all the worksheets.

{% highlight pyhon %}
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
{% endhighlight %}

Image example 

With this little code we are able to get the data to write it into our report with the answers of all the students.

### Repeating the process for multiple files

Then, I need to go through all the files of each student and make the summary and the grading template. For this, I'll place all the files in a folder and then repeat the previous process for each one.

{% highlight pyhon %}
import win32com.client
import glob
import sys, io

# Open up Excel and make it visible (actually you don't need to make it visible)
excel = win32com.client.Dispatch('Excel.Application')
excel.Visible = True

# Select the path of the folder with all the files
files = glob.glob("D:/Users/josepereiran/Desktop/Lab3/LabPrevio/H-0982/*.xlsx")

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

  # Get the answers to the Q2A
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
	print("PREGUNTA 2A")
	print("Misión SI:",mision)
	print("Visión SI:",vision)
	print()
  
  # Get the answers to the Q3A
	print("PREGUNTA 3A")
	for i in range(5,13): 
		proy=wb_data.Worksheets("3a_ProySI").Range("B"+str(i))
		desc=wb_data.Worksheets("3a_ProySI").Range("D"+str(i))
		mcfr=wb_data.Worksheets("3a_ProySI").Range("E"+str(i))
		tipo=wb_data.Worksheets("3a_ProySI").Range("F"+str(i))	
		print("\tProyecto:",proy)
		print("\tDescripción:",desc)
		print("\tMacFarlan:",mcfr,"- Tipo",tipo)
		print()
    
  # Close the file without saving
	wb_data.Close(True)

# Closing Excel and restoring the stdout
sys.stdout = orig_stdout
bk.close()

wb_template = excel.Workbooks.Add()

# Headers
wb_template.Worksheets(1).Range("A1").Value = 'File'
wb_template.Worksheets(1).Range("B1").Value = 'Q1A'
wb_template.Worksheets(1).Range("C1").Value = 'C1A'
wb_template.Worksheets(1).Range("D1").Value = 'Q1B'
wb_template.Worksheets(1).Range("E1").Value = 'C1A'
wb_template.Worksheets(1).Range("F1").Value = 'Q2A'
wb_template.Worksheets(1).Range("G1").Value = 'C2A'

for idx, arch in enumerate(archivos):
	wb_template.Worksheets(1).Range("A"+str(idx+2)).Value = arch.replace('\\','/')	

# Add full path, otherwise it will save in My Documents
excel.DisplayAlerts = False
wb_template.SaveAs(r'd:\Users\josepereiran\Desktop\Grades_Template.xlsx')
wb_template.Close()
excel.DisplayAlerts = True
excel.Quit()

{% endhighlight %}

Finally, I'm able to get the summary of all the files in a little .txt and a template for grading each of the students

Image example 3

### Conclusion
My preference is to try to stick with python as much as possible for my day-to-day data analysis. However, it is important to know when other technologies can streamline the process or make the results have a bigger impact. Microsoft’s COM technology is a mature technology and can be used effectively through python to do tasks that might be too difficult to do otherwise. Hopefully this article has given you some ideas on how to incorporate this technique into your own workflow.

### Next steps
With this example, I showed you how to open an set of Excel files with the same structure form a folder and extract all of its data into a .txt report and into an Excel template to grade the different questions in each of the files.

In the next tutorial, I'll show you how to read template file with the grades and place them and the comments in each of the student files to avoid the work of opening each individual file and working directly in them.
