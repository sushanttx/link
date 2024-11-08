# link
pragma solidity ^0.5.8;

contract FakeBank {
    // Struct to represent an account
    struct Account {
        uint256 balance;
        bool exists;
    }
    
    // Mapping to store accounts by account number
    mapping(uint256 => Account) private accounts;
    
    // Mapping to track which account numbers belong to each user
    mapping(address => uint256[]) private userAccounts;
    
    // Event for account creation
    event AccountCreated(address indexed owner, uint256 accountNumber);
    
    // Event for adding money
    event MoneyAdded(uint256 indexed accountNumber, uint256 amount);
    
    // Event for transaction between accounts
    event Transaction(uint256 indexed fromAccount, uint256 indexed toAccount, uint256 amount);
    
    // Function to create an account with a unique account number
    function createAccount(uint256 accountNumber) public {
        require(!accounts[accountNumber].exists, "Account number already in use.");
        
        accounts[accountNumber] = Account({
            balance: 0,
            exists: true
        });
        
        userAccounts[msg.sender].push(accountNumber);

        emit AccountCreated(msg.sender, accountNumber);
    }
    
    // Function to add "fake" money to a specified account
    function addMoney(uint256 accountNumber, uint256 amount) public {
        require(accounts[accountNumber].exists, "Account does not exist.");
        require(amount > 0, "Amount must be greater than 0.");
        
        accounts[accountNumber].balance += amount;

        emit MoneyAdded(accountNumber, amount);
    }
    
    // Function to check the balance of a specified account
    function checkBalance(uint256 accountNumber) public view returns (uint256) {
        require(accounts[accountNumber].exists, "Account does not exist.");
        return accounts[accountNumber].balance;
    }
    
    // Function to transfer money between two accounts
    function transact(uint256 fromAccount, uint256 toAccount, uint256 amount) public {
        require(accounts[fromAccount].exists, "Sender account does not exist.");
        require(accounts[toAccount].exists, "Recipient account does not exist.");
        require(accounts[fromAccount].balance >= amount, "Insufficient balance.");
        require(amount > 0, "Amount must be greater than 0.");

        accounts[fromAccount].balance -= amount;
        accounts[toAccount].balance += amount;

        emit Transaction(fromAccount, toAccount, amount);
    }
    
    // Function to get all account numbers owned by the caller
    function getUserAccounts() public view returns (uint256[] memory) {
        return userAccounts[msg.sender];
    }
}
