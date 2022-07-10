# GUI application using Tkinter 
<h2> Python app that displays data using a graphical user interface. The interface is generated using the Tkinter library. The app has the functionality to select datasets, displaying the data, displaying graphs, filtering between data, and generate seperate excel reports for each unique identifier.</h2>  


1: This is how the main menu looks like where you have 2 input fields (1 for slecting the dataset and the last to enter the unique identifier value. The start button will generate reports in excel for each unique identifier value (see part 4)

<img width="450" alt="image" src="https://user-images.githubusercontent.com/19918869/176546694-c5a55da8-9113-4842-8502-8e8bf3054cb5.png">

2: This is a drilldown window showing more granular data based on a given specified unique identifier. This window has 2 buttons that generates a bar plot and line plot based on the current identifier (see 3 for the plots)

<img width="320" alt="image" src="https://user-images.githubusercontent.com/19918869/176549858-8e66cb76-b370-4326-948a-db48898777a8.png">

3: A bar and a line plot depicting for the selected unique identifier its values in easy to read and user friendly charts 

<img width="250" alt="image" src="https://user-images.githubusercontent.com/19918869/176549918-b8729269-6789-4171-bae4-b4d1fd611820.png"> <img width="255" alt="image" src="https://user-images.githubusercontent.com/19918869/176549955-b45e31a3-e0dd-4995-95d5-9898f93907c3.png">

4: Generated excel files for each unique identifier present in the dataset. The generated excel files has 2 tabs with one depicting the respetive values and the other having a chart.  
<img width="520" alt="image" src="https://user-images.githubusercontent.com/19918869/176550594-e9885933-da71-45e7-8724-58454008495b.png">

<h2>Included files and how to run the code</h2>
inlcuded in theis project are:
<div>1: The sample dataset on which this application is based. So if you want to use a different dataset, then please adjust the script according to your need.</div>
<div>2: Images used within the application </div>
<div>3: The main script </div>

Just run the script in a Python envrionment. Please make sure to have to have changed all the paths/directories based on your systems directory structure. 


<h2>Script description</h2>
Below here is a brief decription of the different parts of the script used to compose the app. Please be advised that I used in my case a dataset with hard coded variable names. As such you are not using the sample dataset provided, you might want to change the code according to your dataset 
 

<h3>Importing packages</h3>

```python
import tkinter as tk
from tkinter import ttk
import os
from tkinter.tix import ButtonBox 
import numpy as np
import pandas as pd
from pathlib import Path
from matplotlib.backends.backend_tkagg import (FigureCanvasTkAgg, NavigationToolbar2Tk)
from matplotlib.backend_bases import key_press_handler
from matplotlib.figure import Figure
from matplotlib import pyplot as plt
from PIL import Image,ImageTk
from PIL import ImageTk as itk
from tkmacosx import Button
```

<h3> configure TKinter window, set plot style, and specify images location  </h3>

```python
plt.style.use('ggplot')
window = tk.Tk()
window.title('Demo Application')
window.configure(bg='#ffffff')

# Specify the location of the images 
play_img = Image.open('/Applications/XAMPP/xamppfiles/htdocs/Data/images/start.png')
logo = Image.open('/Applications/XAMPP/xamppfiles/htdocs/Data/images/logo1.jpg')
avatar = Image.open('/Applications/XAMPP/xamppfiles/htdocs/Data/images/man.png')

logo_sz = logo.resize((300,200))
play_img_sz = play_img.resize((300,300))
avatar_img_sz = avatar.resize((200,200))
play_fin_img = itk.PhotoImage(play_img_sz)
logo_fin_img = itk.PhotoImage(logo_sz)
avatar_fin_img = itk.PhotoImage(avatar_img_sz)
```

<h3> Functions used within the App </h3>

```python
# Function that outputs excel reports based on the unique identifier, in this case its the hard-coded "CustomerID".
# The values are hard coded here, so this function might require change when using it for your specific case.
def excelprep(choice, path):
    # Specify here the location where to look, otherwise errors might pop up
    df = pd.read_csv('/Applications/XAMPP/xamppfiles/htdocs/Data/'+choice, encoding="utf-8", sep=';')
    # CustomerID is the specifed unique identifier. -> customize based on your need
    for row in zip(df.CustomerId.unique()):
        print(row[0])
        xlxwriter = pd.ExcelWriter(path / str(str(row[0])+'customer_data.xlsx'), engine='xlsxwriter') # define wb with writer fn 
        workbook  = xlxwriter.book
        worksheet1 = workbook.add_worksheet('Customer')

        # formating of the excel report------------
        formating = workbook.add_format({'bold': True, 'font_color':'white', 'bg_color':'black'})
        worksheet1.set_column('A:A',20)
        worksheet1.write('A1', 'Customer ID:', formating)
        worksheet1.write('B1', row[0])
        worksheet1.write('A2', 'Last Name:', formating)
        worksheet1.write('B2', df.loc[df['CustomerId'] == row[0], 'Last_Name'].unique()[0])
        worksheet1.write('A3', 'Date Of birth:', formating)
        worksheet1.write('B3', df.loc[df['CustomerId'] == row[0], 'Age'].unique()[0])
        
    
        df[df.CustomerId ==row[0]].to_excel(xlxwriter, sheet_name='Data', index=False) # adding data to sheet in excel 
        worksheet2 = xlxwriter.sheets['Data']
        
        # example of creating an excel chart
        chart = workbook.add_chart({'type': 'column'})
        chart.add_series({
            'categories': '=Data!$C$2:$C$3',
            'values': '=Data!$D$2:$D$3'})
        worksheet2.insert_chart('A5', chart)
        xlxwriter.save()

# Function that checks whether specified unique identifier exists, if yes than it would procceed to get the right values. 
# The values are hard coded here, so this function might require change when using it for your specific case.
def get_user_data(user_inputted, selected_dataset):
    if user_inputted.isdecimal():  
        user_input = int(user_inputted.strip())
        # Specify here the location where to look, otherwise errors might pop up
        df = pd.read_csv('/Applications/XAMPP/xamppfiles/htdocs/Data/'+selected_dataset, encoding="utf-8", sep=';')
        testing_occurence = df.loc[df['CustomerId'] == user_input]
        if len(testing_occurence) == 0:
            return ['There is no customer with this ID-number']
        else:
            CustomerID = user_input
            Last_name = df.loc[df['CustomerId'] == user_input, 'Last_Name'].unique()[0]
            CreditScore = df.loc[df['CustomerId'] == user_input, 'CreditScore'].unique()[0]
            Gender = df.loc[df['CustomerId'] == user_input, 'Gender'].unique()[0]
            Age = df.loc[df['CustomerId'] == user_input, 'Age'].unique()[0]
            arr_list = df.loc[df['CustomerId'] == user_input, 'arrayu'].unique()[0]
            return [CustomerID, Last_name, CreditScore, Gender, Age, arr_list]
    else:
        return ['There is no customer with this ID-number']



# Unhides buttons and functions whenever a user selects a dataset from the main screen. 
def display_selected(choice):
    choice = variable.get()
    if choice != "select an item" or choice != "":
        show_widget(label_input)
        show_widget(userinput)
        show_widget(button_cus_num)


# When user presses the START button then this will call the required functions 
def fn_all():
    choice = variable.get()
    if choice == "select an item" or choice == "":
        pass
    else:
        name = 'test'
        # Adjust the path based on your directory structure, otherwise errors might pop up
        path = Path('/Applications/XAMPP/xamppfiles/htdocs/Data/EXCELFOLDER_'+ str(name))
        path.mkdir(exist_ok=True)
        excelprep(choice, path) 


# funtion to unhide user select widget 
def show_widget(widget):
    print(widget.winfo_class())
    if widget.winfo_class() == 'Entry':
        widget.pack(side=tk.LEFT,  fill='x')
    elif widget.winfo_class() == 'Label':
        widget.pack(side=tk.LEFT, fill='x')
    else:
        widget.pack(side= tk.BOTTOM, pady=3, expand=True)


# Function that opens a unique identifier details window based on the inputted value in the main window screen 
def openNewWindow():
    newWindow = tk.Toplevel(window)
    user_inputted = userinput.get()
    selected_dataset = variable.get()
    output_result = get_user_data(user_inputted, selected_dataset) #feed into data handler 
    print(user_inputted)
    print(selected_dataset)
    print(output_result)
    if len(output_result) > 1:
        newWindow.title(user_inputted + " detailed information")
        newWindow.geometry("430x450")

        tk.Label(newWindow, image=avatar_fin_img).grid(row=0, column=0, padx=5, pady=5)

        tk.Label(newWindow, text = "Customer ID:", font=('Helvetica', 18 ,'bold'), foreground ='blue').grid(row=1, column=0, padx=5, pady=5)
        tk.Label(newWindow, text = user_inputted, font=('Helvetica', 18)).grid(row=1, column=2, padx=5, pady=5)
    
        tk.Label(newWindow, text = "Last Name:", font=('Helvetica', 18 ,'bold'), foreground ='blue').grid(row=2, column=0, padx=5, pady=5)
        tk.Label(newWindow, text = output_result[1], font=('Helvetica', 18)).grid(row=2, column=2, padx=5, pady=5)

        tk.Label(newWindow, text = "Credit Score:", font=('Helvetica', 18 ,'bold'), foreground ='blue').grid(row=3, column=0, padx=5, pady=5)
        tk.Label(newWindow, text = output_result[2], font=('Helvetica', 18)).grid(row=3, column=2, padx=5, pady=5)

        tk.Label(newWindow, text = "Gender:", font=('Helvetica', 18 ,'bold'), foreground ='blue').grid(row=4, column=0, padx=5, pady=5)
        tk.Label(newWindow, text = output_result[3], font=('Helvetica', 18)).grid(row=4, column=2, padx=5, pady=5)

        tk.Label(newWindow, text = "Date of birth:", font=('Helvetica', 18 ,'bold'), foreground ='blue').grid(row=5, column=0, padx=5, pady=5)
        tk.Label(newWindow, text = output_result[4], font=('Helvetica', 18)).grid(row=5, column=2, padx=5, pady=5)

        button_plt = tk.Button(newWindow, text="Display credit health", width=15, height=1, fg="black", command=plotWindow)
        button_plt.config(font=('Helvetica', 18, 'bold'), borderwidth='1', highlightthickness=2, pady=2)
        button_plt.grid(row=7, column=0, padx=5, pady=5)
        button_plt = tk.Button(newWindow, text="Display spend history", width=15, height=1, fg="black", command=plotWindow_series)
        button_plt.config(font=('Helvetica', 18, 'bold'), borderwidth='1', highlightthickness=2, pady=2)
        button_plt.grid(row=7, column=2, padx=5, pady=5)

    else:
        newWindow.title("Error")
        newWindow.geometry("150x100")
        tk.Label(newWindow, text = "Nothing found").pack()


# Display a bar chart based on the inputted unique identifier in the main screen  
def plotWindow():
    pltWindow = tk.Toplevel(window)
    pltWindow.title( "bar chart credit score")
    pltWindow.geometry("500x500")
    user_inputted = userinput.get()
    selected_dataset = variable.get()
    output_result = get_user_data(user_inputted, selected_dataset) 

    x = [0,2,4]
    y =[0,output_result[2],0]

    fig, ax = plt.subplots(1, 1)

    ax.bar(x, y, color='blue', alpha=0.7)
    ax.set_title(f'Contact:{output_result[1]}, Credit score is: {output_result[2]}', fontsize=20)
    ax.axhline(y=690, color='green',linestyle='--',label ='Sufficient',linewidth=3)
    ax.axhline(y=300, color='red',linestyle='--',label ='Unsufficient',linewidth=3)
    ax.set_ylabel('Credit score', fontsize=10)
    ax.legend(prop={'size':14})
    ax.axes.xaxis.set_ticks([])
    # plt.show()

    canvas = FigureCanvasTkAgg(fig, master=pltWindow)  # A tk.DrawingArea.
    canvas.draw()
    canvas.get_tk_widget().pack(side=tk.TOP, fill=tk.BOTH, expand=1)

    toolbar = NavigationToolbar2Tk(canvas, pltWindow)
    toolbar.update()
    canvas.get_tk_widget().pack(side=tk.TOP, fill=tk.BOTH, expand=1)

# Display a line chart based on the inputted unique identifier in the main screen  
def plotWindow_series():
    pltWindow = tk.Toplevel(window)
    pltWindow.title( "Expenditures per period")
    pltWindow.geometry("500x500")
    user_inputted = userinput.get()
    selected_dataset = variable.get()
    output_result = get_user_data(user_inputted, selected_dataset) 

    x = ['2019','2020','2021','2022']
    y = output_result[5].split(',')
    y_int = [int(i) for i in y]

    fig, ax = plt.subplots(1, 1)

    ax.plot(x, y_int, color='blue', alpha=0.7, linewidth=3)
    ax.set_title(f'Yearly expenditures {output_result[1]}', fontsize=20)
    ax.set_ylabel('Expenditures in Euro', fontsize=10)
    ax.set_xlabel('Year', fontsize=10)

    canvas = FigureCanvasTkAgg(fig, master=pltWindow)  # A tk.DrawingArea.
    canvas.draw()
    canvas.get_tk_widget().pack(side=tk.TOP, fill=tk.BOTH, expand=1)

    toolbar = NavigationToolbar2Tk(canvas, pltWindow)
    toolbar.update()
    canvas.get_tk_widget().pack(side=tk.TOP, fill=tk.BOTH, expand=1)


# Styling applied to buttons when hovering over them with mouse 
def changeOnHover(button, colorOnHover, colorOnLeave):
    button.bind("<Enter>", func=lambda e: button.config(highlightbackground=colorOnHover))
    button.bind("<Enter>", func=lambda e: button.config(fg=colorOnHover))
    button.bind("<Leave>", func=lambda e: button.config(highlightbackground=colorOnLeave))
    button.bind("<Leave>", func=lambda e: button.config(fg=colorOnLeave))
```

<h3> Initial preperation when starting App </h3>

```python
# List up the CSV option in a list 
# Adjust the path based on your directory structure, otherwise app might not start 
path = '/Applications/XAMPP/xamppfiles/htdocs/Data'
OptionList =[]

for files in  os.scandir(path): 
    if files.name.endswith('.csv') == True:
        OptionList.append(files.name)

window.geometry('500x400')
variable = tk.StringVar(window)
variable.set("select an item")
```

<h3> Widgets used in the App </h3>

```python
# label for logo 
w = tk.Label(window, image=logo_fin_img)
w.config(bg="#ffffff") 
w.pack()

# Option menu for selecting the CSV 
opt=tk.OptionMenu(window, variable, *OptionList, command=display_selected)
opt.config(width=90, font=('Helvetica', 18))
opt.pack()

# The input field where users can input an unique identifier value 
userinput = tk.Entry(window)
label_input = tk.Label(window, text = "Enter user_ID:", font=('Helvetica', 18),highlightbackground='white',highlightthickness=0)


# Button to initiate run, the START button
button_run_all = tk.Button(image = play_fin_img,height=120,  borderwidth=0, command=fn_all) #fg="#3E4149"
button_run_all.config(background="white", bd = 0, highlightthickness = 0,  highlightbackground='#fff', highlightcolor='#fff', borderwidth=0) 
button_run_all.pack(pady=3)

# Button to get a drill down window based on inputted unique identifier value 
button_cus_num = tk.Button(text="Get Customer", width=15, height=1, fg="black", command=openNewWindow)
button_cus_num.config(font=('Helvetica', 18, 'bold'), borderwidth='1', highlightthickness=2, pady=2)

changeOnHover(button_cus_num, "#45bbff", "#3E4149")

window.mainloop()
```
