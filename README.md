#include<bits/stdc++.h>
#include<conio.h>
using namespace std;
const int num_of_patients = 100;
const int num_of_doctors = 10;
int times = 7;
int thisuserindex = 0;
int currentuserindex = -1;
int currentdoctorindex = -1;
int thisdoctorindex = 0;
string currentusertype = "";

struct account {
    string username;
    string password;
};

struct user {
    account useraccount;
    string name;
    int ID;
};

struct doctor {
    user doctoraccount;
    string specialization;
    string available_time[7];
    bool appointment[7] = { false };
    string patient_names[7];
};
user users[num_of_patients];
doctor doctors[num_of_doctors];

bool signupusers(user users[], int size);
bool signupdoctors(doctor doctors[], int size);
bool findusernamedoctor(string username, doctor users[]);
bool findusernameuser(string username, user users[]);
bool finduserid(int id, user users[]);
bool findDoctorID(int id, doctor doctors[]);
string passwordinput();
bool findloginuser(string username, string password, user users[]);
bool findlogindoctor(string username, string password, doctor doctors[]);
bool login();
void viewdoctors();
void addtime(doctor doctors[], int time);
void removetime(doctor doctors[]);
void edittime(doctor doctors[]);
void viewtime(doctor doctors[]);
void book(doctor doctors[]);
void viewapp(doctor doctors[]);
void removeapp(doctor doctors[]);
void removeapphistory(doctor doctors[]);
void viewpatientswithappointments();
void displayAvailableDoctorsAtTime(string time);
void edituserusername(user users[]);
void edituserpassword(user users[]);
void editdoctorusername(doctor doctors[]);
void editdoctorpassword(doctor doctors[]);
void writeusers(user users[], int size);
void readusers(user users[], int& size);
void writedoctor(doctor doctors[], int size);
void readdoctor(doctor doctors[], int& size);
void welcome();
void doctorf();
void patientf();

int main() {
    cout << "\t\t\t ================================ \n";
    cout << "\t\t\t Welcome to Rehabilitation Center \n";
    cout << "\t\t\t ================================ \n\n";
    int userCount = 0, doctorCount = 0;
    char choice1, choice2;
    // Load previous data
    readusers(users, userCount);
    readdoctor(doctors, doctorCount);
    thisuserindex = userCount;
    thisdoctorindex = doctorCount;
    do {
        welcome();
    } while (!login());
    if (currentusertype == "doctor") {
        do {
            doctorf();
            cout << "Press any key to logout\nPress Y to keep using your account.";
            cin >> choice1;
            if (choice1 != 'y' && choice1 != 'Y') {
                cout << "Logged out...";
            }
        } while (choice1=='y' || choice1=='Y');
    }
    else if (currentusertype == "patient") {
        do {
            patientf();
            cout << "Press any key to logout\nPress Y to keep using your account.";
            cin >> choice2;
            if (choice2 != 'y' && choice2 != 'Y') {
                cout << "Logged out...";
            }
        } while (choice2 == 'y' || choice2 == 'Y');
    }
    else {
        cout << "No valid user is currently logged in.\n";
    }

    // Save data back to files
    writeusers(users, thisuserindex);
    writedoctor(doctors, thisdoctorindex);
    return 0;
}

bool signupusers(user users[], int size) /*checked*/ {
    int i = thisuserindex;
    cout << "Enter your Username: \n";
    cin >> users[i].useraccount.username;
    if (findusernameuser(users[i].useraccount.username, users)) {
        cout << "This username already exists \n";
        return false;
    }
    cout << "Enter your password: \n";
    users[i].useraccount.password = passwordinput();
    cout << "Enter your name: \n";
    cin >> users[i].name;
    do {
        users[i].ID = (rand() % 100) + 1;
    } while (finduserid(users[i].ID, users));
    cout << "Your id is:" << users[i].ID << endl;
    thisuserindex++;
    return true;
}

bool signupdoctors(doctor doctors[], int size)/*checked*/ {
    int i = thisdoctorindex;
    cout << "Enter your Username: \n";
    cin >> doctors[i].doctoraccount.useraccount.username;
    if (findusernamedoctor(doctors[i].doctoraccount.useraccount.username, doctors)) {
        cout << "This username already exists \n";
        return false;
    }
    cout << "Enter your password: \n";
    doctors[i].doctoraccount.useraccount.password = passwordinput();
    cout << "Enter your name: \n";
    cin >> doctors[i].doctoraccount.name;
    do {
        doctors[i].doctoraccount.ID = (rand() % 10) + 1;
    } while (findDoctorID(doctors[i].doctoraccount.ID, doctors));
    cout << "Your id is:" << doctors[i].doctoraccount.ID << endl;
    cout << "Enter your speciality: \n";
    cin >> doctors[i].specialization;
    thisdoctorindex++;
    return true;
}

bool findusernamedoctor(string username, doctor doctors[]) {
    for (int i = 0; i < thisdoctorindex;i++) {
        if (username == doctors[i].doctoraccount.useraccount.username) {
            return true;
        }
    }
    return false;
}

bool findusernameuser(string username, user users[]) {
    for (int i = 0; i < thisuserindex;i++) {
        if (username == users[i].useraccount.username) {
            return true;
        }
    }
    return false;
}

bool findloginuser(string username, string password, user users[]) {
    for (int i = 0; i < thisuserindex;i++) {
        if (username == users[i].useraccount.username && password == users[i].useraccount.password) {
            currentuserindex = i;
            return true;
        }
    }
    return false;
}

bool findlogindoctor(string username, string password, doctor doctors[]) {
    for (int i = 0; i < thisdoctorindex;i++) {
        if (username == doctors[i].doctoraccount.useraccount.username && password == doctors[i].doctoraccount.useraccount.password) {
            currentdoctorindex = i;
            return true;
        }
    }
    return false;
}


string passwordinput()/*checked*/ {
    string input = "";
    char ch;
    while (true) {
        ch = _getch();
        if (ch == '\r') {
            break;
        }
        else if (ch == '\b') {
             input.pop_back();
             cout << "\b \b";
        }
        else {
            input += ch;
            cout << '*';
     }
    }
    cout << endl;
    return input;
}

bool login() {
    string username, password;
    cout << "Enter your Username: \n";
    cin >> username;
    cout << "Enter your password: \n";
    password = passwordinput();

    if (findloginuser(username, password, users) || findlogindoctor(username, password, doctors)) {
        cout << "Logged in successfully \n";
        if (findloginuser(username, password, users)) {
            currentusertype = "patient";
        }
        else if (findlogindoctor(username, password, doctors)) {
            currentusertype = "doctor";
        }
        return true;
    }
    else {
        cout << "Invalid username or password \n";
        return false;
    }
}

bool finduserid(int id, user users[]) {
    for (int i = 0; i < thisuserindex;i++) {
        if (users[i].ID == id) {
            return true;
        }
    }
    return false;
}

bool findDoctorID(int id, doctor doctors[]) {
    for (int i = 0; i < thisdoctorindex; i++)
        if (doctors[i].doctoraccount.ID == id) {
            return true;
        }
    return false;
}

void viewdoctors() {
    for (int i = 0; i < thisdoctorindex; i++) {
        cout << i+1 << ". " << doctors[i].doctoraccount.name <<"|" << doctors[i].specialization << endl;
    }
}

void addtime(doctor doctors[], int time)/*checked*/ {
    for (int i = 0; i < times; i++) {
        cout << i+1 << ". ";
        cin >> doctors[currentdoctorindex].available_time[i];
        cout << endl;
    }
}

void removetime(doctor doctors[])/*checked*/ {
    int i;//index of time
    cout << "remove" <<endl;
    cin >> i;
    i -= 1;
    for (int j = i; j < times - 1; ++j) {
        doctors[currentdoctorindex].available_time[j] = doctors[currentdoctorindex].available_time[j + 1];
    }
    times--;
    viewtime(doctors);
}

void edittime(doctor doctors[])/*checked*/ {
    string temp;//time wanted to be put
    int i;//index of time to be edited
    cout << "enter i";
    cin >> i;
    i -= 1;
    cout << "temp";
    cin >> temp;
    doctors[currentdoctorindex].available_time[i] = temp;
    viewtime(doctors);
}

void viewtime(doctor doctors[]) {
    for (int i = 0;i < times;i++) {
        cout << i+1 << ". " << doctors[currentdoctorindex].available_time[i];
        cout << endl;
    }
}

void book(doctor doctors[]) {
    int doctor_index, slot_index;
     cout << "Enter Doctor Index (1 to " << thisdoctorindex << "): ";
    cin >> doctor_index;
    doctor_index -= 1;
    cout << "Enter Time Slot (1 to " << times << "): ";
    cin >> slot_index;
    slot_index -= 1;
    if (doctors[doctor_index].appointment[slot_index]) {
          cout << "Time slot already booked.\n";
        return;
    }
    string patient_name;
    cout << "Enter your name: ";
    cin >> patient_name;
    doctors[doctor_index].appointment[slot_index] = true;
    doctors[doctor_index].patient_names[slot_index] = patient_name;
    cout << "Appointment booked with Dr. " << doctors[doctor_index].doctoraccount.name
        << " at " << doctors[doctor_index].available_time[slot_index] << "\n";
}

void viewapp(doctor doctors[]) {
    for (int i = 0; i < thisdoctorindex; i++) {
        for (int j = 0; j < times; j++) {
            if (doctors[i].appointment[j]) {
                cout << doctors[i].available_time[j] << " booked by " << doctors[i].patient_names[j] << endl;
            }
        }
    }
}

void removeapp(doctor doctors[]) {
    int doctor_index, slot_index;
    cout << "Enter Doctor Index (1 to " << thisdoctorindex << "): ";
    cin >> doctor_index;
    doctor_index -= 1;
    cout << "Enter Time Slot (1 to " << times << "): ";
    cin >> slot_index;
    slot_index -= 1;
    doctors[doctor_index].appointment[slot_index] = false;
    doctors[doctor_index].patient_names[slot_index] = "";
}

void removeapphistory(doctor doctors[]) {
    for (int i = 0;i < times;i++) {
        doctors[currentdoctorindex].appointment[i] = false;
        doctors[currentdoctorindex].patient_names[i] = "";
    }
}

void viewpatientswithappointments() {
    for (int i = 0; i < thisdoctorindex; ++i) {
        cout << "Doctor: " << doctors[i].doctoraccount.name << " (" << doctors[i].specialization << ")\n";
        bool hasAppointments = false;
        for (int j = 0; j < times; ++j) {
            if (doctors[i].appointment[j]) {
                cout << "  Time: " << doctors[i].available_time[j]
                    << " | Patient: " << doctors[i].patient_names[j] << "\n";
                hasAppointments = true;
            }
        }
        if (!hasAppointments) {
            cout << "  No appointments.\n";
        }
        cout << "-------------------------\n";
    }
}

void displayAvailableDoctorsAtTime(string time) {
    cin >> time;
    bool found = false;
    cout << "Doctors available at " << time << ":\n";
    for (int i = 0; i < thisdoctorindex; ++i) {
        for (int j = 0; j < times; ++j) {
            if (doctors[i].available_time[j] == time && !doctors[i].appointment[j]) {
                cout << "Dr. " << doctors[i].doctoraccount.name
                    << " | Specialization: " << doctors[i].specialization
                    << " | Time Slot Index: " << j + 1 << "\n";
                found = true;
                break; // No need to check other time slots
            }
        }
    }
    if (!found) {
        cout << "No doctors available at that time.\n";
    }
}

void edituserusername(user users[]) {
    int index = currentuserindex;
    if (index < 0) {
        cout << "No user is currently logged in.\n";
        return;
    }
    string newusername;
    cout << "Enter new username: ";
    cin >> newusername;
    if (findusernameuser(newusername, users)) {
        cout << "This username is already taken.\n";
    }
    else {
        users[index].useraccount.username = newusername;
        cout << "Username updated successfully!\n";
    }
}

void edituserpassword(user users[]) {
    int index = currentuserindex;
    cout << "Enter new password: ";
    users[index].useraccount.password = passwordinput();
    cout << "Password updated successfully!\n";
}

void editdoctorusername(doctor doctors[]) {
    int index = currentdoctorindex;
    if (index < 0) {
        cout << "No doctor is currently logged in.\n";
        return;
    }
    string newusername;
    cout << "Enter new username: ";
    cin >> newusername;
    if (findusernamedoctor(newusername, doctors)) {
        cout << "This username is already taken.\n";
    }
    else {
        doctors[index].doctoraccount.useraccount.username = newusername;
        cout << "Username updated successfully!\n";
    }
}

void editdoctorpassword(doctor doctors[]) {
    int index = currentdoctorindex;
    cout << "Enter new password: ";
    doctors[index].doctoraccount.useraccount.password = passwordinput();
    cout << "Password updated successfully!\n";
}

void writeusers(user users[], int size) {
    fstream out("userdata.txt", ios::out);
    if (!out) {
        cout << "File not Found";
        return;
    }
    for (int i = 0; i < size; i++) {
        out << users[i].useraccount.username
            << " " << users[i].useraccount.password
            << " " << users[i].name << " "
            << users[i].ID << endl;
    }
    out.close();
}

void readusers(user users[], int& size) {
    fstream in("userdata.txt", ios::in);
    if (!in) {
        cout << "File not Found";
        return;
    }
    string username, password, name;
    int ID;
    size = 0;
    while (in >> username >> password >> name >> ID) {
        users[size].useraccount.username = username;
        users[size].useraccount.password = password;
        users[size].name = name;
        users[size].ID = ID;
        size++;
    }
    in.close();
}

void writedoctor(doctor doctors[], int size) {
    fstream out("doctordata.txt", ios::out);
    if (!out) {
        //cout << "File not Found";
        return;
    }
    for (int i = 0; i < size; i++) {
        out << doctors[i].doctoraccount.useraccount.username << " "
            << doctors[i].doctoraccount.useraccount.password << " "
            << doctors[i].doctoraccount.name << " "
            << doctors[i].doctoraccount.ID << " "
            << doctors[i].specialization << " ";
        for (int j = 0; j < times; j++) {
            out << doctors[i].available_time[j] << " ";
        }
        out << endl;
    }
    out.close();
}

void readdoctor(doctor doctors[], int& size) {
    fstream in("doctordata.txt", ios::in);
    if (!in) {
        //cout << "File not Found";
        return;
    }
    string username, password, name, specialization, time;
    int ID;
    size = 0;
    while (in >> username >> password >> name >> ID >> specialization) {
        doctors[size].doctoraccount.useraccount.username = username;
        doctors[size].doctoraccount.useraccount.password = password;
        doctors[size].doctoraccount.name = name;
        doctors[size].doctoraccount.ID = ID;
        doctors[size].specialization = specialization;
        for (int j = 0; j < times; j++) {
            in >> time;
            doctors[size].available_time[j] = time;
        }
        size++;
    }
    in.close();
}

void welcome() {
    int choice;
    cout << "Choose: \n";
    cout << "1. To Signup as a doctor.\n";
    cout << "2. To Signup as a patient.\n";
    cout << "3. To Log in.\n";
    cout << "Enter your choice: ";
    cin >> choice;
    if (choice == 1) {
        signupdoctors(doctors, num_of_doctors);
    }
    else if (choice == 2) {
        signupusers(users, num_of_patients);
    }
    else if (choice == 3) {
        login();
    }
    else {
        cout << "Invalid Choice \n";
    }
}

void doctorf() {
    int choice , choice2;
    cout << "Choose: \n";
    cout << "1. Add available time\n";
    cout << "2. Remove available time\n";
    cout << "3. Edit available time\n";
    cout << "4. View Patients with appointments\n";
    cout << "5. Edit (Username/Password)\n";
    cout << "Enter your choice: ";
    cin >> choice;
    if (choice == 1) {
        addtime(doctors, times);
    }
    else if (choice == 2) {
        removetime(doctors);
    }
    else if (choice == 3) {
        edittime(doctors);
    }
    else if (choice == 4) { 
        viewpatientswithappointments(); 
    }
    else if(choice==5){
        cout << "Choose: \n";
        cout << "1. Edit your username\n";
        cout << "2. Edit your password\n";
        cout << "Enter your choice: ";
        cin >> choice2;
        if (choice2 == 1) {
            editdoctorusername(doctors);
        }
        else if (choice2 == 2) {
            editdoctorpassword(doctors);
        }
        else {
            cout << "Invalid Choice \n";
        }
    }
    else{
        cout << "Invalid Choice \n";
    }
}

void patientf() {
    int choice, choice2;
    cout << "Choose: \n";
    cout << "1. View Doctors\n";
    cout << "2. Display doctor available in certain appointment time\n";
    cout << "3. Book an appointment\n";
    cout << "4. View Appointments\n";
    cout << "5. Delete Appointments\n";
    cout << "6. Delete Appointments History\n";
    cout << "7. Edit (Username/Password)";
    cout << "Enter your choice: ";
    cin >> choice;
    if (choice == 1) {
        viewdoctors();
    }
    else if (choice == 2) {
        string time;
        displayAvailableDoctorsAtTime(time);
    }
    else if (choice==3) {
        book(doctors);
    }
    else if (choice == 4) {
        viewapp(doctors);
    }
    else if (choice == 5) {
        removeapp(doctors);
    }
    else if (choice == 6) {
        removeapphistory(doctors);
    }
    else if (choice == 7) {
        cout << "Choose: \n";
        cout << "1. Edit your username\n";
        cout << "2. Edit your password\n";
        cout << "Enter your choice: ";
        cin >> choice2;
        if (choice == 1) {
            edituserusername(users);
        }
        else if (choice == 2) {
            edituserpassword(users);
        }
        else {
            cout << "Invalid Choice \n";
        }
    }
    else {
        cout << "Invalid Choice \n";
    }
}
