import time
from tkinter import *
from tkinter import messagebox, filedialog
import pandas
import mysql.connector

# Connect to MySQL (adjust the parameters as needed)
con = mysql.connector.connect(host="localhost", user="root", password="password", database="student_db")
mycursor = con.cursor()

# Initialize Tkinter
root = Tk()
root.geometry('1200x700+200+50')
root.title('Student Management System')
root.config(bg='gold2')
root.iconbitmap('man.ico')

# Main Frame
DataEntryFrame = Frame(root, bg='gold2', relief=GROOVE, borderwidth=5)
DataEntryFrame.place(x=10, y=80, width=500, height=600)

# Function to add student
def addstudent():
    def submitadd():
        id = idval.get()
        name = nameval.get()
        mobile = mobileval.get()
        email = emailval.get()
        address = addressval.get()
        gender = genderval.get()
        dob = dobval.get()
        addedtime = time.strftime("%H:%M:%S")
        addeddate = time.strftime("%d/%m/%Y")
        try:
            strr = 'INSERT INTO studentdata1 VALUES(%s,%s,%s,%s,%s,%s,%s,%s,%s)'
            mycursor.execute(strr, (id, name, mobile, email, address, gender, dob, addeddate, addedtime))
            con.commit()
            res = messagebox.askyesnocancel('Notifications', f'Id {id} Name {name} Added successfully... Do you want to clear the form?', parent=addroot)
            if res:
                for var in [idval, nameval, mobileval, emailval, addressval, genderval, dobval]:
                    var.set('')
        except mysql.connector.Error as err:
            messagebox.showerror('Notifications', f'Error: {err}', parent=addroot)
        
        # Refresh the student table
        refresh_table()

    addroot = Toplevel(master=DataEntryFrame)
    addroot.grab_set()
    addroot.geometry('470x470+220+200')
    addroot.title('Student Management System')
    addroot.config(bg='blue')
    addroot.iconbitmap('man.ico')
    addroot.resizable(False, False)

    # Labels and Entries for Add Student Form
    labels = ['Enter Id:', 'Enter Name:', 'Enter Mobile:', 'Enter Email:', 'Enter Address:', 'Enter Gender:', 'Enter D.O.B:']
    y_positions = [10, 70, 130, 190, 250, 310, 370]
    entry_vars = [StringVar() for _ in labels]
    for label_text, y_pos, entry_var in zip(labels, y_positions, entry_vars):
        Label(addroot, text=label_text, bg='gold2', font=('times', 20, 'bold'), relief=GROOVE, borderwidth=3, width=12, anchor='w').place(x=10, y=y_pos)
        Entry(addroot, font=('roman', 15, 'bold'), bd=5, textvariable=entry_var).place(x=250, y=y_pos)

    idval, nameval, mobileval, emailval, addressval, genderval, dobval = entry_vars

    Button(addroot, text='Submit', font=('roman', 15, 'bold'), width=20, bd=5, activebackground='blue', activeforeground='white', bg='red', command=submitadd).place(x=150, y=420)

    addroot.mainloop()

# Function to refresh the student table
def refresh_table():
    strr = 'SELECT * FROM studentdata1'
    mycursor.execute(strr)
    datas = mycursor.fetchall()
    studenttable.delete(*studenttable.get_children())
    for data in datas:
        studenttable.insert('', END, values=data)

# Define the student table
studenttable = ttk.Treeview(root, columns=('ID', 'Name', 'Mobile', 'Email', 'Address', 'Gender', 'D.O.B', 'Date', 'Time'), show='headings')
studenttable.pack()

# Other functions (searchstudent, deletestudent, updatestudent, showstudent, exportstudent) should be structured similarly

root.mainloop()
