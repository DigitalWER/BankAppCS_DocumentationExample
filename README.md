# BankAppCS
**Short documentation sample**  
**Author:** Marcin Wilczyński

## Table of Contents
1. [Description](#description)
2. [Features](#features)  
    a. [Operations between application windows](#operationsWindows)  
    b. [LogIn Window](#logInWindow)  
    c. [User LogIn procedure](#userLogIn)  
    d. [New User registration window](#userRegistration)  
    e. [Main menu after LogIn](#mainMenu)  
    f. [Bank Account Wizard](#bankAccWizard)
3. [Considerations and Guidelines](#Considerations)


## Description <a name="description"></a>
<div align="adjust">BankApp is an application based on banking functionality. It is designed as a window application using .NET technology. It allows the creation of a new user through a registration procedure and after logging in, there is an option to open a bank account in the chosen currency and perform financial operations on it. Banking functions such as withdraw or deposit a money amount, the ability to create multiple bank accounts and visibility of user details and its bank accounts in the main menu are included inside the application.</div>

## Features <a name="features"></a>
### Operations between application windows <a name="operationsWindows"></a>
<div align="adjust">Each window relates to each other by using buttons. For each of them an appropriate function for the next or the previous window is implemented, which allows moving between windows. The buttons are labelled in the visible application window according to their function.</div>

#### Example 1 – movement between windows, start window -> sign up window
```groovy
private void SignUpOpen(object sender, RoutedEventArgs e)
        {
            RegistrationWindow objOpenWindow = new RegistrationWindow();
            this.Close();
            objOpenWindow.Show();
        }
```

<div align="adjust">After logging in, a similar variant of navigating the designed window structure is used, except that the logged-in user is initialized in the window constructor, so that the performed operations concern a specific application user.</div>

#### Example 2 – movement between windows when user is logged in, main window –> create bank account window

```groovy
public partial class UserMenuWindow : Window
    {
        private User user;
        public UserMenuWindow(User user)
        {
            this.user = user;
            InitializeComponent();
            OperationBox.Items.Add("Deposit");
            OperationBox.Items.Add("Withdraw");
            OperationBox.SelectedIndex = 1;
            LabelName.Content = user.getFirstName();
            LastName.Content = user.getLastName();
            Email.Content = user.getEmail();
            Username.Content = user.getUsername();
            resetDataGrid();
        }

[…] some other code […]

private void openCreateAccountWindow(object sender, RoutedEventArgs e)
        {
            CreateAccountWindow objOpenWindow = new CreateAccountWindow(user);
            this.Close();
            objOpenWindow.Show();
        }
```



### Log In window <a name="logInWindow"></a>
<div align="adjust">The initial window of the application is the login window. To take full advantage of the designed functionality it is necessary to log into the application. In order to test the application, one permanent user has been saved, whose access with username and password is respectively: username: admin, password: admin (it is initialized in the constructor of the start window)</div>

```groovy
public StartWindowSignIn()
        {
            InitializeComponent();
            users.Add(new User("admin", "admin", 111222333, "admin@mail.com", "admin", "admin"));
        }
```
#### User object construction
```groovy
public User(string firstName, string lastName, int phoneNumber, string email, string username, string password)
```

### User Log In procedure <a name="userLogIn"></a>
<div align="adjust">The applied textboxes take the input data from the application user (username and password) in purpose to verify it with the previously created user account. The entered data are compared with the data existing in the list (inside the User class). If the data matches, the login is successful and the user is transferred into the main user window, otherwise a message about incorrect login data is shown.</div>
  
#### User Log In procedure
```groovy
private void SignInButton_Click(object sender, RoutedEventArgs e)
        {
            bool succesfulLogin = false;
            foreach (User user in users)
            {
                if (string.Equals(user.getUsername(), UsernameTextbox.Text))
                {
                    if (string.Equals(user.getPassword(), PassowrdPassowrdBox.Password))
                    {
                        UserMenuWindow objOpenWindow = new UserMenuWindow(user);
                        succesfulLogin = true;
                        objOpenWindow.Show();
                        this.Close();
                        break;
                    }
                }
            }
            if (!succesfulLogin)
                MessageBox.Show("Incorrect login or password");
        }
```


### New User registration window <a name="userRegistration"></a>
<div align="adjust">The data entered by the person that creates the account are retrieved by the application using appropriately described textboxes. After that, the entered data are validated (currently only the password and phone number are being verified). If the user registration is correct, the registration window is closed with an appropriate message about a correct registration. Otherwise, a message about possibly incorrectly filled data is displayed. Correctly entered data are called in the constructor of the user class to create a new user, and then it is saved to the list of registered users. After creating a new user, it is possible to log in to the application and use the banking functionality.</div>

#### Registration Window code:
```groovy
public partial class RegistrationWindow : Window
    {
        private string _password;
        private string _checkPassword;
        public RegistrationWindow()
        {
            InitializeComponent();
        }

        private void OpenStartWindow(object sender, RoutedEventArgs e)
        {
            StartWindowSignIn objOpenWindow = new StartWindowSignIn();
            this.Close();
            objOpenWindow.Show();
        }

        private void SignUpAndOpenMainPage(object sender, RoutedEventArgs e)
        {
            _password = password.Password;
            _checkPassword = checkPassword.Password;
            if (_password.Equals(_checkPassword) && _password != "" && int.TryParse(this.phoneNumber.Text, out int phoneNumber) && this.phoneNumber.Text.Length == 9)
            {
                User newUser = new User(firstName.Text, lastName.Text, phoneNumber, email.Text, username.Text, password.Password);
                OpenStartWindow(sender, e);
                MessageBox.Show("New user is created! You can log in now.");
            }
            else
            {
                MessageBox.Show("Inputted passwords doesn't match or some inputs are incorrect. Please check your inputs again.");
            }
            
        }
    }
```

### Main menu after LogIn <a name="mainMenu"></a>
<div align="adjust">This is the main user operation window, where information about the user and connected bank accounts are displayed. The window shows information about the user in the form of text fields that retrieve information from the User class for the logged-in user . A datagrid is also used to visualize existing user accounts with bank account information.The user can select a bank account in the table, view the amount of funds in the account and perform money operations on the selected bank account.
The user can select a bank account in the table, view the amount of funds in the account and perform monetary operations on the selected bank account.
The main menu is capable of performing operations on a given bank account - withdraw money from the account and making a cash deposit.</div>

  #### Withdraw/Deposit operations

Money operations are implemented in a way that uses a comboBox, a field for typing the appropriate amount of money and a button which confirms the operation. To perform an operation, it is also necessary to select an account from the datagrid where all user accounts are displayed.
    - With the option of depositing funds, the money is added to the bank account. 
    - When the option of withdrawing funds from the selected account, a verification is performed whether the user has sufficient funds on the chosen account. In case of insufficient funds on the account after an attempt to perform an operation, an appropriate information message pops up
Successful completion of the operation will result in the information about the success of the transaction as well as refreshing and updating the window with information about the account(s).
#### Withdraw/Deposit code:
```groovy
private void OperationButton_Click(object sender, RoutedEventArgs e)
        {
            if (moneyAmountOperation.Text != "")
            {
                if (accountListDataGrid.SelectedItem != null)
                {
                    if ((string)OperationBox.SelectedItem == "Deposit")
                    {
                        Account selectedAccount = (Account)accountListDataGrid.SelectedItem;
                        selectedAccount.Deposit(double.Parse(moneyAmountOperation.Text));
                        user.GetTransactionHistory.Add(new TransactionHistory("Deposit", selectedAccount, double.Parse(moneyAmountOperation.Text), selectedAccount.MainCurrency));
                        MessageBox.Show("Deposit is completed");
                    }
                    else
                    {
                        Account selectedAccount = (Account)accountListDataGrid.SelectedItem;
                        if (!selectedAccount.Withdraw(double.Parse(moneyAmountOperation.Text)))
                        {
                            MessageBox.Show("Not enough money in the account to complete the operation");
                        }
                        else
                        {
                            user.GetTransactionHistory.Add(new TransactionHistory("Withdraw", selectedAccount, double.Parse(moneyAmountOperation.Text), selectedAccount.MainCurrency));
                            MessageBox.Show("Operation completed");
                        }
                    }
                    resetDataGrid();
                }
            }
        }
```


#### Main menu code including navigation between windows and a method for refreshing the field with created bank accounts
```groovy
public partial class UserMenuWindow : Window
    {
        private User user;
        public UserMenuWindow(User user)
        {
            this.user = user;
            InitializeComponent();
            OperationBox.Items.Add("Deposit");
            OperationBox.Items.Add("Withdraw");
            OperationBox.SelectedIndex = 1;
            LabelName.Content = user.getFirstName();
            LastName.Content = user.getLastName();
            Email.Content = user.getEmail();
            Username.Content = user.getUsername();
            resetDataGrid();
        }

        private void OperationButton_Click(object sender, RoutedEventArgs e)
        {
            [...]
        }

        private void logOutAndOpenStartWindow(object sender, RoutedEventArgs e)
        {
            StartWindowSignIn objOpenWindow = new StartWindowSignIn();
            this.Close();
            objOpenWindow.Show();
            MessageBox.Show("You have logged out successfully");
        }

        private void openCreateAccountWindow(object sender, RoutedEventArgs e)
        {
            CreateAccountWindow objOpenWindow = new CreateAccountWindow(user);
            this.Close();
            objOpenWindow.Show();
        }

        private void openTransactionHistoryWindow(object sender, RoutedEventArgs e)
        {
            TransactionHistoryWindow objOpenWindow = new TransactionHistoryWindow(user);
            this.Close();
            objOpenWindow.Show();
        }

        private void resetDataGrid()
        {
            accountListDataGrid.ItemsSource = null;
            accountListDataGrid.ItemsSource = user.Accounts;
        }
    }
```

### Bank Account Wizard <a name="bankAccWizard"></a>

<div align="adjust">In the bank account wizard, there are options to set the name of the new account, the currency of the bank account and a possible function to select whether the account is to be a multi-currency account.  Implemented function under the button, named "create account" creates a bank account by calling the constructor of the Account class and saves the newly created account on the list of accounts for the currently logged in user. After saving, the main window opens where the new account is displayed in a datagrid.</div>

#### Create Account Window code

```groovy
public partial class CreateAccountWindow : Window
    {
        private User user;
        public CreateAccountWindow(User user)
        {
            this.user = user;
            InitializeComponent();
            OperationBox.Items.Add("PLN");
            OperationBox.Items.Add("EUR");
            OperationBox.Items.Add("USD");
            OperationBox.SelectedIndex = 0;
        }
        public CreateAccountWindow()
        {
            InitializeComponent();
        }

        private void multiCurrnecy(object sender, RoutedEventArgs e)
        {

        }

        private void createAccount(object sender, RoutedEventArgs e)
        {
            if (newAccountName.Text.Equals(""))
            {
                MessageBox.Show("Error! New account must be named");
            }
            else
            {
                Account newAccount;
                if (checkMultiCurrency.IsChecked == true)
                {
                    newAccount = new MultiCurrencyAccount(newAccountName.Text, OperationBox.Text);
                }
                else
                {
                    newAccount = new DefaultAccount(newAccountName.Text, OperationBox.Text);
                }
                user.Accounts.Add(newAccount);
                MessageBox.Show("Account is created successfully");
                OpenMainWindow(sender, e);
            }
        }

        private void OpenMainWindow(object sender, RoutedEventArgs e)
        {
            UserMenuWindow objOpenWindow = new UserMenuWindow(user);
            this.Close();
            objOpenWindow.Show();
        }
    }    
```


## Considerations and Guidelines <a name="Considerations"></a>

### Summary of the current state

<div align="adjust">The application is still under development. The core functionality of the banking application has been covered, which allows for further development of the source code. The code is written in C# language using WPF based methodology. The implementation of the data extracted by the application will be continued by the creation of a SQL database.</div>
 

