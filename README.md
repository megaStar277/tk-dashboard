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


<h2>Script explanation</h2>
####################################################################################################
    # 1 IMPORT PACKAGES 
####################################################################################################
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
