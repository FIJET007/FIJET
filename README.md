from PyQt5.QtCore import *
from PyQt5.QtGui import *
from PyQt5.QtWidgets import *
from PyQt5.uic import loadUi
import sys
import sqlite3
import re


db = sqlite3.connect("database-car2.db")
cur = db.cursor()


class FirstScreen(QDialog):
    # initiating the object
    def __init__(self, parent=None):
        super(FirstScreen, self).__init__(parent)
#         load the ui file
        loadUi("Screen 1.ui", self)
        self.Staff_id_entry = self.findChild(QLineEdit, "staffidbox")
        self.username_entry = self.findChild(QLineEdit, "usernamebox")
        self.password_entry = self.findChild(QLineEdit, "passwordbox")
        self.login_button = self.findChild(QPushButton, "loginbtn")
        self.signup_button = self.findChild(QPushButton, "signupbtn")
        self.errormessage = self.findChild(QLabel, "errorbox")
        # THIS IS TO MAKE THE FUNCTION DOSOMETHING TO RUN WHEN CLICKED ON LOGIN OR SIGNUP
        self.login_button.clicked.connect(self.dosomething)
        self.open()

    def dosomething(self):
        id = self.Staff_id_entry.text()
        username = self.username_entry.text()
        password = self.password_entry.text()
        password_regex = r"^(?=.*[a-zA-Z0-9])(?=.*[\W_]).{8,}$"

        if not all([id, username, password]):
            self.errormessage.setText("ALL FIELDS MUST BE FILLED")

        elif not re.match(password_regex, password):
            self.errormessage.setText("INVALID PASSWORD")
            self.password_entry.setText(None)

        else:
            cur.execute("SELECT * FROM Staff WHERE Staff_id = ? and Username = ? and Password = ?",
                        (id, username, password))

            if cur.fetchall():
                message = QMessageBox()
                message.setFixedSize(800, 800)
                message.setIcon(QMessageBox.Information)

                message.setText("LOGIN SUCCESSFUL")
                message.exec_()

                self.hide()
                es = SecondScreen(self)
                es.show()
                es.exec_()

            else:
                message = QMessageBox()
                message.setFixedSize(800, 800)
                message.setIcon = QMessageBox.warning
                message.setText("invalid login details")
                message.exec_()


class SecondScreen(QDialog):
    def __init__(self, parent=None):
        super(SecondScreen, self).__init__(parent)
        loadUi("Screen 2.ui", self)
        self.sales_btn = self.findChild(QPushButton, "salesbtn")
        self.vehicles_btn = self.findChild(QPushButton, "vehiclesbtn")
        self.customers_btn = self.findChild(QPushButton, "customersbtn")
        self.back_btn = self.findChild(QPushButton, "backbtn")

        self.vehicles_btn.clicked.connect(self.gotovehicles)
        self.back_btn.clicked.connect(self.goback)
        self.open()

    def goback(self):
        self.hide()
        aa = FirstScreen()
        aa.show()
        aa.exec_()

    def gotovehicles(self):
        self.hide()
        aa = ThirdScreen(self)
        aa.show()
        aa.exec_()


class ThirdScreen(QDialog):
    def __init__(self, parent=None):
        super(ThirdScreen, self).__init__(parent)
        loadUi("Screen 3.ui", self)
        self.add_btn = self.findChild(QPushButton, "addbutton")
        self.edit_btn = self.findChild(QPushButton, "editbutton")
        self.search_btn = self.findChild(QPushButton, "searchbutton")
        self.delete_btn = self.findChild(QPushButton, "deletebutton")
        self.back_btn = self.findChild(QPushButton, "backbutton")

        self.back_btn.clicked.connect(self.goback)
        self.edit_btn.clicked.connect(self.gotoedit)
        self.add_btn.clicked.connect(self.gotoadd)
        self.search_btn.clicked.connect(self.gotosearch)
        self.open()

    def goback(self):
        self.hide()
        aa = SecondScreen(self)
        aa.show()
        aa.exec_()

    def gotoedit(self):
        self.hide()
        aa = FifthScreen(self)
        aa.show()
        aa.exec_()

    def gotoadd(self):
        self.hide()
        aa = FourthScreen(self)
        aa.show()
        aa.exec_()

    def gotosearch(self):
        self.hide()
        aa = SixthScreen(self)
        aa.show()
        aa.exec_()


class FourthScreen(QDialog):
    def __init__(self, parent=None):
        super(FourthScreen, self).__init__(parent)
        loadUi("Screen 4.ui", self)
        self.back_button = self.findChild(QPushButton, "backbutton")
        self.done_button = self.findChild(QPushButton, "donebutton")
        self.brand_input = self.findChild(QLineEdit, "brand")
        self.model_input = self.findChild(QLineEdit, "model")
        self.year_input = self.findChild(QLineEdit, "year")
        self.colour_input = self.findChild(QLineEdit, "colour")

        self.done_button.clicked.connect(self.addtodata)
        self.back_button.clicked.connect(self.gotoback)
        self.open()

    def addtodata(self):
        brandvalue = self.brand_input.text()
        modelvalue = self.model_input.text()
        yearvalue = self.year_input.text()
        colourvalue = self.colour_input.text()

        if not all([brandvalue, modelvalue, yearvalue, colourvalue]):
            print("ALL VALUES MUST BE FILLED")
            return

        if not re.match(r"\d{4}", yearvalue):
            if not 1886 <= int(yearvalue) <= 2024:
                print("Year must be a number between 1886 and 2024")
                return

        colours = ["White", "Blue", "Grey", "Black"]
        if colourvalue.capitalize() not in colours:
            print("Invalid colour")
            return

        cur.execute("INSERT INTO Vehicles (Brand, Model, Year, Colour) VALUES (?, ?, ?, ?)",
                    (brandvalue, modelvalue, yearvalue, colourvalue))
        db.commit()

        msg = QMessageBox()
        msg.setFixedSize(800, 800)
        msg.setIcon(QMessageBox.Information)

        msg.setText("Vehicle Added Successfully")
        msg.exec_()

        self.hide()
        aa = ThirdScreen(self)
        aa.show()
        aa.exec_()

    def gotoback(self):
        self.hide()
        aa = ThirdScreen(self)
        aa.show()
        aa.exec_()


class FifthScreen(QDialog):
    def __init__(self, parent=None):
        super(FifthScreen, self).__init__(parent)
        loadUi("Screen 5.ui", self)
        self.back_button = self.findChild(QPushButton, "backbutton")
        self.load_button = self.findChild(QPushButton, "loadbutton")
        self.done_button = self.findChild(QPushButton, "donebutton")
        self.vehicle_id = self.findChild(QLineEdit, "vehicleid")
        self.brand_input = self.findChild(QLineEdit, "brand")
        self.model_input = self.findChild(QLineEdit, "model")
        self.year_input = self.findChild(QLineEdit, "year")
        self.colour_input = self.findChild(QLineEdit, "colour")
        self.error_message = self.findChild(QLabel, "errorbox")

        self.initial_values =[]

        self.back_button.clicked.connect(self.gotoback)
        self.load_button.clicked.connect(self.loaddetails)
        self.done_button.clicked.connect(self.makechanges)
        self.open()


    def loaddetails(self):
        vec_id = self.vehicle_id.text()
        if not re.match(r"\d", vec_id):
            print("VALUE MUST BE A NUMBER")

        elif vec_id == "":
            print("PLEASE TYPE IN A VEHICLE ID")
        else:
            cur.execute("SELECT * FROM Vehicles WHERE Vehicle_id = ?", (vec_id,))
            details = cur.fetchall()
            if details:
                self.brand_input.setText(details[0][1])
                self.model_input.setText(details[0][2])
                self.year_input.setText(str(details[0][3]))
                self.colour_input.setText(details[0][4])

                self.initial_values = [details[0][1], details[0][2], details[0][3], details[0][4]]

            else:
                print("INVALID VEHICLE ID. TRY AGAIN")
                self.brand_input.setText(None)
                self.model_input.setText(None)
                self.year_input.setText(None)
                self.colour_input.setText(None)


    def makechanges(self):
        vehic_id = self.vehicle_id.text()
        brand_name = self.brand_input.text()
        model_name = self.model_input.text()
        year_name = self.year_input.text()
        colour_name = self.colour_input.text()

        if not all([brand_name, model_name, year_name, colour_name]):
            print("ALL FIELDS MUST BE FILLED")
            return

        if not re.match(r"\d{4}", year_name):
            if not 1886 <= int(year_name) <= 2024:
                print("Year must be a number between 1886 and 2024")
                return

        colours = ["White", "Blue", "Grey", "Black"]
        if colour_name.capitalize() not in colours:
            print("Invalid colour")
            return

        new_values = [brand_name, model_name, year_name, colour_name]

        if new_values == self.initial_values:
            print("NO CHANGE DETECTED")

        cur.execute("UPDATE Vehicles SET Brand = ?, Model = ?, Year = ?, Colour = ? WHERE Vehicle_id = ?",
                    (new_values[0], new_values[1], new_values[2], new_values[3], vehic_id))
        db.commit()

        msg = QMessageBox()
        msg.setFixedSize(800, 800)

        msg.setIcon(QMessageBox.Information)
        msg.setText("DETAILS Update Successfully")
        msg.exec_()

        self.hide()
        bb = ThirdScreen(self)
        bb.show()
        bb.exec_()

        self.initial_values = new_values

    def gotoback(self):
        self.hide()
        bb = ThirdScreen(self)
        bb.show()
        bb.exec_()


class SixthScreen(QDialog):
    def __init__(self, parent=None):
        super(SixthScreen, self).__init__(parent)
        loadUi("Screen 6.ui", self)
        self.tablestuff = self.findChild(QTableWidget, "table")
        self.backbutton = self.findChild(QPushButton, "back_btn")
        self.searchbutton = self.findChild(QPushButton, "search_btn")
        self.searchbox = self.findChild(QLineEdit, "search_field")
        self.combo = self.findChild(QComboBox, "options")
        self.editbutton = self.findChild(QPushButton, "edit_btn")
        self.deletebutton = self.findChild(QPushButton, "delete_btn")

        self.backbutton.clicked.connect(self.gotoback)
        self.searchbutton.clicked.connect(self.searchdata)
        self.editbutton.clicked.connect(self.gotoedit)

        cur.execute("SELECT * FROM Vehicles")
        result = cur.fetchall()

        row = 0
        self.tablestuff.setRowCount(len(result))
        for car in result:
            self.tablestuff.setItem(row,  0, QTableWidgetItem(str(car[0])))
            self.tablestuff.setItem(row,  1, QTableWidgetItem(car[1]))
            self.tablestuff.setItem(row,  2, QTableWidgetItem(car[2]))
            self.tablestuff.setItem(row,  3, QTableWidgetItem(str(car[3])))
            self.tablestuff.setItem(row,  4, QTableWidgetItem(car[4]))

            row += 1

    def searchdata(self):
        query = self.searchbox.text()
        if self.combo.currentText() == "Vehicle ID":
            cur.execute("SELECT * FROM Vehicles WHERE Vehicle_id = ?", (query,))
        elif self.combo.currentText() == "Brand":
            cur.execute("SELECT * FROM Vehicles WHERE Brand = ?", (query,))
        elif self.combo.currentText() == "Model":
            cur.execute("SELECT * FROM Vehicles WHERE Model = ?", (query,))
        elif self.combo.currentText() == "Year":
            cur.execute("SELECT * FROM Vehicles WHERE Year = ?", (query,))

        result = cur.fetchall()

        row = 0
        self.tablestuff.setRowCount(len(result))
        for car in result:
            self.tablestuff.setItem(row,  0, QTableWidgetItem(str(car[0])))
            self.tablestuff.setItem(row,  1, QTableWidgetItem(car[1]))
            self.tablestuff.setItem(row,  2, QTableWidgetItem(car[2]))
            self.tablestuff.setItem(row,  3, QTableWidgetItem(str(car[3])))
            self.tablestuff.setItem(row,  4, QTableWidgetItem(car[4]))

            row += 1
        self.searchbox.setText(None)

    def gotoback(self):
        self.hide()
        bb = ThirdScreen(self)
        bb.show()
        bb.exec_()

    def gotoedit(self):
        self.hide()
        bb = FifthScreen(self)
        bb.hide()
        bb.exec_()

def ogapatapata():
    hey = QApplication(sys.argv)
    hi = FirstScreen()
    hi.show()
    sys, exit(hey.exec_())


if __name__ == "__main__":
    ogapatapata()

