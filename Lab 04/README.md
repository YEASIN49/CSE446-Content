
# Lab 4 : Ethereum Private Network and Smart Contract Interaction

## Objective: 
-   To create an ethereum private network and portray a scenario of how the network interacts in the real world. Students will also learn the basics of smart contract deploy and how to communicate with it.

## Submission: 
-   Four Checkpoints/Tasks need to be shown to the course teacher during the lab.

## Introduction:

In this lab, you will learn how to create and interact with an ethereum private network using Go Ethereum(geth) implementation. In addition, students will learn how to use Remix IDE for developing smart contracts. Finally they will learn how to interact with smart contracts from local test networks using geth javascript console.

Ethereum is an open source blockchain which facilitates the features of smart contracts. In the ethereum network hundreds of computers known as Ethereum Nodes get connected and create a peer to peer network that runs client software to store, validate and create blocks. Geth is one of these clients. To interact or to create such a network Go Ethereum(geth) implementation is widely popular. Go Ethereum also known as geth is the official go implementation of the ethereum protocol written in golang. Geth provides many client side tools for which it is thought as the standard for other ethereum nodes.

In this lab, we will use the Proof-of-Authority consensus of ethereum known as clique. Therefore, in PoA consensus the concept of miner is not relevent. Here, selaers are the node/account that add/publish/sign/validate the block into the blockchain. _However, for the simplicity, we will mention sealer/miner interchangeably in this lab._

To develop, test and debug smart contracts using solidity language, Remix IDE is considered to be the best IDE among the blockchain developer community. It is considered as the industry standard  for its rich set of plugins, debug features having intuitive Graphical User Interface.

There are four tasks that you will need to complete in this lab. **_Complete all the tasks and show it to your teacher._**  At the end of this lab students should know - 
-   How to set up nodes for the private ethereum network
-   How to join network and interact
-   Basics of Remix IDE
-   Deploy and interact with smart contract in local ethereum private network

# Task-1: Development Environment Setup

In this lab, we will utilize the Ubuntu development environment. As it turns out, Ubuntu and Mac has rich support for Ethereum development tools. In recent times, these tools are also being made available for Windows. However, in this tutorial we will utilize Ubuntu. At first, you will setup your machine for the development.

## 1. Node Installation

To run the geth console, we need to install nodejs. You already know what nodejs is from your previous lab. Now, check if you already have nodejs and stable npm installed on your machine and using the following command:

```
npm version
```
This command will print an object. If node is installed, its version number will be printed like node: 18.15.0. I am using version v18.15.0 and therefore the terminal showing it. Currently the LTS NodeJs version is v18.16.1 and should work as well. It is recommended to use any version that does not exceed the current LTS version of nodejs. Therefore if your nodejs and npm versions are stable you don’t need to install it again. Recommended node version <=  v18.16.x and  npm version <= v9.3.1.

**If you do not see the version number printed, you need to install node.** You can install node from any of the methods described below. However, if you have node installed you can **skip node installation** section and jump to **Install Ethereum and Other Needed Tools** section.

### Method-1: By using NVM
We recommend using NVM(Node Version Manager) through which we can install and use and manage any node version we want. This is a very useful tool for managing and working with different node versions we want. It helps to shift to any node version we want within minutes using just one command. To install node using nvm  use the following commands:

```
 sudo apt install curl
```
and 
```
curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
```

The nvm installer script creates an environment entry to the login script of the current user. You can either log out and login again to load the environment or execute the below command to do the same.

```
source ~/.bashrc
```

Now, install any node version you want by following command: $  nvm install YourVersion
For Example: to install node v18.16.1 use command:

```
nvm install 18.16.1. 
```

Now, use command: 
```
nvm ls    
```
and you will see that some node versions are installed. Just select a recommended(v18.15.0 or 18.16.1) version from there by using commande:
```nvm use YOUR-DESIRED-VERSION``` 
for example:  ```nvm use v18.16.1```

Now, check node version by using command:  ```node -v```
You will see your selected version will appear in the terminal.

### Method-2: Direct Installation

To install node directly, use the following command:

```
sudo apt-get update
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash - &&\
sudo apt-get install -y nodejs

```

Ensure that node has been properly installed by checking its version. Also, check if npm has
been installed as well using the following command: ``` npm version```

Thiscommand should show a version in the terminal.

## 2. Install Ethereum and Other Needed Tools

You can install ethereum and other necessary tools using the following commands: 

-       sudo apt-get install software-properties-common
-       sudo add-apt-repository -y ppa:ethereum/ethereum
-       sudo apt-get update
-       sudo apt-get install ethereum


Test your Ethereum installation with the following command:  ```geth version```
This should return versions details similar to the picture below: 

![App Screenshot](./_readme-image/image1.png)

**_Checkpoint 1: Show the result to your teacher_**

# Task-2: Ethereum Private Network Setup

In this section you will be setting up a simple private network of two ethereum nodes which will be running in your local mechine. For the rest of the sections , we will mention ethereum nodes as node. 

1. For this, create a folder called **_ethereumNetwork_**. Assume this is our root folder. Go to that folder and right click to open the terminal from there. Remember we will do all of our tasks inside this folder.

2. Now, create two directories for the two nodes by following command: 
```
mkdir node1 node2 
```
3. Now we will create a separate account for each of the nodes by following command:

For **Node1**:
```
geth --datadir node1 account new
```
This will ask for a password. After entering the password the following information is returned to the terminal.

![App Screenshot](./_readme-image/image2.png)

Copy the public address of the key from the terminal and paste it in a place where you can find it later.

Now, for the account creation of **Node2** use the below command and enter a password similar to the account creation of node1. 
```
geth --datadir node2 account new
```
Again copy and paste the public address of the key in a place where you can find it later.

These two commands create a directory in each node1 and node2 directory called keystore that contains the secret key of the respective account.


4. Now, we will create one file in each node1 and node2 folder to save the password in those files. We will save passwords in those files so that in future we can utilise the file to unlock and access servise from accounts. Therefore, to create a file called password.txt in both node1 and node2 folder use the following command: 
```
touch node1/password.txt node2/password.txt 
```
This will create a **password.txt** file in both the node1 and node2 folder. Open the files, enter only your password for node1 and node2 accounts in their own password.txt file and save it. **For example:** if your password is **1234**, you should write only **1234**.

5. Now, we need to create a genesis file. A genesis file is basically a json file that contains the details of the genesis block, defining the initial state of your blockchain network configuration. This is stored in the very first block of blockchain. A simple genesis file of PoA consensus(AKA Clique in ethereum) contains: 

-   Network configuration 
-   A conesus algorithm: Ethash(Proof-of-Work in ethereum) or Clique(Proof-of-Authority).
-   Sealer Accounts: Can be mentioned more than one account as sealers.
-   Pre-fund Accounts:  Accounts that will be pre funded with ethers.

Now, use the command to create a json file where will will store the configurations.

```
touch genesis.json
```
This will create a json file called _genesis.json_ in the _ethereumNetwork_ folder.

A simple genesis file is provided below for this lab. Now, copy the configuration from below and paste it to the _genesis.json_ file

```json
{
  "config": {
    "chainId": 15,
    "homesteadBlock": 0,
    "eip150Block": 0,
    "eip155Block": 0,
    "eip158Block": 0,
    "byzantiumBlock": 0,
    "constantinopleBlock": 0,
    "petersburgBlock": 0,
    "clique": {
      "period": 30,
      "epoch": 30000
    }
  },
  "difficulty": "1",
  "gasLimit": "8000000",
  "extradata":  "0x00000000000000000000000000000000000000000000000000000000000000004bdEbff0ef88D6D01389ba1E88739c34FF07ec320000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  "alloc": {
    "0x4bdEbff0ef88D6D01389ba1E88739c34FF07ec32": {
      "balance": "300000000000000000000000000000000"
    }
  }
}
```
Here in the genesis file, the config field holds the configuraion of the network and it's chain, extradata holds the information of sealers and alloc holds the data if there are any account which need to have initial balance when the network gets created.

6. Now we will see how we can add sealer from the very first moment of our blockchain network using the **extradata** field of the genesis file. Sealers are the account that can add/sign block to the blockchain. It is similar to the miner that we know from bitcoin. However, in PoA sealer add/sign/publish the block. **In this lab, we will make the first account of node1 as a sealer(Full Node) and rest will be a light node that cannot sign/mine blocks from the pool.** Therefore, to add sealer, **Copy your account public address of node1 without the 0x prefix** that you stored in a safe place earlier in step 3. Now, paste it to the exact place where the 40 characters sealer account is mentioned in the image below:

![App Screenshot](./_readme-image/image3.png)

7. For this lab, we also want to provide some balance(pre-fund) initially to the account of node1. To do this, simply copy the public address of the account of node1 and paste it by replacing the to the address provided in the **alloc** field mentioned in the above image. But this time including the prefix: 0x. 


8. Now, we can configure the both nodes so that they can join the same network using the genesis.json file. To do it enter the command:

```
 geth init --datadir node1 genesis.json
```

If you do it successfully you should a see a response similar to the image below:

![App Screenshot](./_readme-image/image4.png)

Now, Repeat the command for node2 just replacing node1 to node2. These commands will write the genesis configuration in a folder named as “geth” for the private network in both the node1 and node2 folder.

9. Now we will create a dedicated “Bootnode”. One of the basic benefits of bootnode is that it helps peers to find each other's nodes in the same network. Bootnodes require a key to be executed and to generate a key enter the command mentioned below that will create a boot.key file in your ethereumNetwork folder. 
```
bootnode -genkey boot.key
```

10. Now, we will start the bootnode. To start a bootnode we must provide a key which we already have and also we need to assign it to a port as it continuously runs and helps ethereum nodes to find peers in the network. Now, enter the command below to start the boot node:
```
bootnode -nodekey boot.key -addr :30305
```

Here, the port flag **-addr** can be arbitrary, however there are ports that are being used by others. For example ethereum mainnet uses port 30303. Therefore, we should avoid it. For our case we can use port 30305, 30306, 30307 etc. Now, if you have entered the command correctly you should see a log like below. There is a long string called enode. **Copy the whole enode and save it in a place you can find later**. We will see the use of this enode in the upcoming section. Therefore, If you see this, you have successfully initiated the bootnode in running state. **Now leave this terminal window untouched and open a new terminal from the same working directory which is ethereumNetwork folder and do the rest of the task there**.  

![App Screenshot](./_readme-image/image9.png)

11. Finally, we can initiate our private ethereum nodes and connect them in the network. But for this, don’t close the terminal where bootnode is running. We should continue working in a new terminal tab. You can create new terminal tab by clicking the **+** icon on the top left corner of your terminal like below:

![App Screenshot](./_readme-image/add-terminal-tab.jpg)

Now to start our node1 we need to  enter  the following command  shown below:

```shell
geth --datadir node1 --port 30306 --bootnodes Your_Enode_of_the_Bootnode --networkid 12345 --unlock Your_Node1_Account_Public_Address --password node1/password.txt --authrpc.port 8551 --http --http.port 8545 --http.corsdomain "http://localhost:8000" --allow-insecure-unlock
```

![App Screenshot](./_readme-image/image10.png)

This command will run the node1 and connect it with the bootnode that we have 
created earlier. In the command --bootnodes  flag takes enode as parameter and the enode that we have provided here is the enode of the bootnode. Since our bootnode has occupied the port 30305,  we used --port 30306 here. We have used the network id flag --networkid 12345 here. If you can remember, “12345” is the chainID of our genesis file configuration. Moreover, we see  --unlock  flag holding an address like this: ```0xC730c18424F6Cac66E1529256535b7db53B0Da23```

If I Check the file where I have stored the public address of account of node1 and I will find this is the same address. You should use your public account address key here. Furthermore, the **--password** flag is pointing to a file which is in node1/password.txt. This is the file where we stored the password of the account of node1. In addition, for the flag **--http.corsdomain** we used “http://localhost:8000”, this is because we will visualize our blockchain transactions in that url in the upcoming section. After writing down the command, you should see logs similar to the picture attached below which 
refers that your node1 is now connected in the network:

![App Screenshot](./_readme-image/image11.png)

12. Now keep this log running and leave the terminal untouched. Open a new terminal tab and enter the following command to connect the node2 with the bootnode so that it can join the network also. The command is shown below:

```shell
geth --datadir node2 --port 30307 --bootnodes Your_Enode_of_the_Bootnode --networkid 12345 --unlock Your_Node2_Account_Public_Address --password node2/password.txt --authrpc.port 8552 --http --http.port 8546 --http.corsdomain "http://localhost:8000" --allow-insecure-unlock
```


![App Screenshot](./_readme-image/image12.png)

After connecting and joining the network leave this terminal window untouched so that node2 keeps running. Here, we used **--port 30307** since 30305 and 30306 are already occupied by bootnode and node1 respectively. Secondly, **--authrpc.port 8552** since 8551 is already occupied by node1. Thirdly **--http.port 8546** used because the node1 occupied the the port 8545. 

Finally, if everything is ok, **you should see your peercount = 1** in your terminal now. If you don’t see it, there are some steps that you have mistaken. Try to debug what have you done wrong.

![App Screenshot](./_readme-image/image12_1.png)

**_It should be remembered that if we want to connect a new node, these parameters must point to another port for that new node._**

Finally, now you have a local ethereum private network with two nodes. 

**_Checkpoint: 2: Show the result to your teacher_**

## Task-3: Network Interaction
In this task, you will learn how to attach nodes with geth javascript console so that you can perform various actions inside the peer to peer network. Before going further, we must ensure our bootenode, node1 and node2 are running as we did in the previous section. Now  to move forward, first we will initiate the geth javascript console for node1 and node2.

1. Open a fresh new terminal tab or window and enter the command below to initiate the geth console for node1.
```
geth attach node1/geth.ipc
```
This will open a console for executing javascript code for node1 in our network. Open another 
terminal and enter the same command but instead of node1, do it for node2. If you do it 
correctly, you should see a console like below.

![App Screenshot](./_readme-image/image13.png)

Notice there are some modules along with their version number mentioned in the 
console.  Among them admin:1.0, clique:1.0, eth:1.0, miner:1.0, txpool:1.0, web3:1.0 etc. are some important modules that you can use in this geth console. We will show you some transactions so that you can have a general idea how cryptocurrency exchanges work in real life underneath using some of these modules.

2. We can check the list of our account addresses and balance using the functions below:   
```
 eth.accounts
 ```
 and, for example: ```eth.getBalance(eth.accounts[0])```

 ```
eth.getBalance(eth.accounts[ accountIndexNumber ])
 ```
Initially both of our nodes contain one account each. However we can create and use multiple accounts in a single ethereum node. If you check the balance of your node2 account, you will see the balance is zero. However, the account of node1 has some amount of balance. Now, try to remember, while creating the genesis file we added the account address of node1 for prefunding. This balance has generated from that prefunding genesis configuration.

3. Now in a new terminal create a new account using the following fucnction command that we used earlier:
```
geth --datadir node1 account new
```
This will create a new account in node1. Now, in the console of node1, check accounts by entering:
```
eth.accounts
```

If you do it correctly, you should see two accounts now.

4. Check balance of the new account from node1 console by entering:  
```
eth.getBalance(eth.accounts[1])
```
This will print the balance of new account. Currently the new account does not have any balance. So we will send some ether(wei) from our account1 to the new account of node1. For this, call the following function: 
```
eth.sendTransaction({ from: eth.accounts[0], to: eth.accounts[1], value: 5000 })
```
After entering the command, check the running network log of node1. You should see a submitted transaction request there like the picture below: 

![App Screenshot](./_readme-image/image14.png)

Now copy the hash of the submitted request and check the balance of the new account. It is still zero. This is because our transaction request is now waiting to be mined/sign by miners/sealers.

**Note:** To sign/mine block in Ethereum, a node need to be a sealer/signer. Currently the first account of node1 is the only sealer account we have in the network. Try to remember, we added the account as a sealer in the genesis.json file. Therefore, In the upcoming section, we will seal/mine block from this account, since other accounts are not allowed to do this. 

5. To visualize the process in real life we will use a block explorer. First, open a new termina tab and download/clone the explorer using the command inside your ethereumNetwork folder:
```shell
git clone https://github.com/etherparty/explorer.git
```
Now, go inside the explorer folder using command ```cd explorer``` and write ```npm start```. This will start running a live block explorer of our network in http://localhost:8000. This is the reason we used this same url for **--http.corsdomain** flag while initializing the networks. Open the explorer from your browser from the mentioned url and initially you will see only one block there. This is the genesis block.

6. Now, from your geth console of node1, we will start mining/signing block. However, first we need to set the sealer account as etherbase account. To do this use the command:

```shell
miner.setEtherbase(eth.accounts[0])
```

Also, we will set our first account as the default account. To do this run the command provided below:

```shell
eth.defaultAccount = eth.accounts[0]
```

Now, we can start mining using the command:
```
miner.start()
```

You should see some logs in the running network log terminal of node1 mentioning mining blocks like the pictures below. If you don’t see the logs, wait for a couple of minutes. Sometimes it takes time to start mining.

![App Screenshot](./_readme-image/image15.png)

7. Now, if you check explorer in http://localhost:8000 , Refresh the page and you will see, some new blocks are being created. Now from the same console where you started mining, enter: 
```
miner.stop()
```

This will stop the mining process of node1 and if you refresh the explorer page you will see no new blocks are being created. Check the explorer, there should be one block where **Tx#** column will have value 1. This means the block contains one transaction which is basically the transaction that we made to send ethers(wei) from one account to another. You should explore this explorer to understand transaction information. In real life, we can see this kind of information in explorers like https://etherscan.io . 

![App Screenshot](./_readme-image/image16.png)

8. Now, again check the balance of the new account of node1 and you should see the value is 5000. Congratulations, you have just completed your first transaction. You can check the transaction details using transactionHash that you copied in step 4 using the command:  ```eth.getTransaction(“transaction-hash”)``` , here transaction-hash is the hash value that we copied from the submitted request. Now compare these details with the information you get from the explorer.  

    **_Checkpoint 3: Show the task that you have done till now, to your teacher._**

9. To practice at your home, you can find all the available functions provided in geth console just by typing the module name in the geth console. For Example, type eth and press enter, you will see all the available functions of eth object. Similarly, check the functions of web3, miner, admin, clique etc.

## Task-4: Deploy and Interact with Smart Contract

Now, we are ready to look at the smart contract development process. We will look at the process in detailed fashion. But before that I would like you to go through the following steps in which you will code, deploy and interact with an Ethereum smart contract.

1. The first step is to write a smart contract using Solidity. Solidity is a high-level programming language which is heavily used for writing smart-contracts for different blockchain  platforms. Similar to Java it needs to be compiled using a compiler to create a bytecode. To write and compile solidity, we will utilize **Remix IDE** which is a web-based solidity Editor and Compiler platform. It is located on: https://remix.ethereum.org . Go to the link and you will see a folder named contracts on the left side of the screen. Click the folder you will see some files with .sol extension. These are some demo smart contracts.  

![App Screenshot](./_readme-image/image16_1.png)

2. Now, create a file called SimpleContract.sol in Redmix IDE and copy the provided smart contract code below and paste it there. You will know more, about smart contract and remix in the upcoming classes/labs

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.9.0;


contract SimpleContract {


   string userName = "Lab is almost done" ;


   function setName(string memory name) public {
       userName = name;
   }


   function getName() public view returns (string memory){
       return userName;
   }
}
```

3. From the compiler section page of Remix, select the version 0.8.7 and then click Start compile. If the code is compiled successfully, you will see a green tick mark there like mention in the above image. This program is very simple and similar to a Java program. A solidity program usually starts with the pragma keyword which defines the required compiler version. Different solidity compilers have different capabilities and different ways to express similar things. Therefore, it is important to set the correct compiler version. Then it defines the contract name, similar to the Java class. In this program we have just two methods and a public variable. The “setName” method with the supplied parameter is used to set the value of the “userName” variable. On the other hand,  the “getName” method is used to retrieve the current value of the “userName” variable. In particular, notice the way functions are defined in Solidity. The memory keyword specifies the data storage type. There are mainly two data storage types in Solidity: memory and storage. If you are confused regarding the code, don't worry, we will know about them later in our course. 

![App Screenshot](./_readme-image/image16_2.png)

4. After successful compilation, click to the ““BYTECODE”” button, this will copy a large data. 

![App Screenshot](./_readme-image/image16_3.png)

Now, paste it into a **txt** file. Avoid using **doc** file for this since saving in doc format may create extra line break and spaces in the data.

5. Now, go to the geth console of node1 or node2. Let’s choose node1. Assign the copied bytecode code with a **prefix 0x** in a variable called contractHex. For example: ```contractHex= “0x60806040526040518060400160405........."```

6. In the Remix IDE, you will also find an option called ABI which contains some json data. Click to copy the ABI from there. 

![App Screenshot](./_readme-image/image16_5.png)

Now we need to format the value by removing extra spaces from the copied data  in order to use it. To do it, we can use the formatter https://codebeautify.org/remove-extra-spaces where we need to paste the ABI and remove the extra spaces. You can use any other json formatter if you want. Now copy the formatted value and assign it to a variable in geth console. For our lab, lets assign the value to a variable called “contractABI” like the image below: 

![App Screenshot](./_readme-image/image19.png)

7. Now, create a contract interface by following line of code: 

![App Screenshot](./_readme-image/image20.png)

8. Finally the contract can now be published in our local network. For this, write: 

![App Screenshot](./_readme-image/image21.png)

In the above code, transaction fee will be deducted from eth.accounts[0] account.

9. Now, this will submit a transaction request to be mined/sealed. Now start the miner from the first accout console of node1 using ```miner.start()``` and when it is mined stop the miner using ```miner.stop()```. You can check if the transaction is minded or still pending using multiple command such as:

```shell
eth.pendingTransactions
```
This will be empty if your transaction is sealed/mined

Alternatively you can check using:

```shell
txpool.content
```

If successfully added to chain, you will find the transaction in the explorer. To interact with the smart contract we must know the address of the smart contract where it is deployed. We can find the address of the deployed smart contract using the transaction hash. To do this, write: 

![App Screenshot](./_readme-image/image22.png)

10. Now we can get the address of the deployed smart contract instance by following: 

![App Screenshot](./_readme-image/image23.png)

11. To interact with the methods of smart contract we can utilize the ```contractInterface``` that we already created in the terminal. In addition we need the ```deployedContractAddress``` through which the interface can create a reference point of the deployed contract so that we can interact with it. To do follow command provided:

```shell
contractRef = contractInterface.at(deployedContractAddress)
```

This will return a result like below:

![App Screenshot](./_readme-image/contract-ref.png)


12. Finally, using this reference we can call methods of our deployed smart contract by it’s address that we saved earlier in a variable ```deployedContractAddress```

![App Screenshot](./_readme-image/almost-done.png)

Here we can see the initial value of our smart contract gets printed.

13. Now we also can set userName using the setName method by following: 

![App Screenshot](./_readme-image/set-name.png)

Note: How do we determine the gas value ? you can run the command below to get the estimated gas value you need to provide.
```shell
eth.estimateGas({ data: contractHex })
```

This will submit a transaction request to be mined. Therefore, if we call the “getName” now, we will not see the updated value. The value will not be updated until the transaction is mined. To check it we can run:
```shell
txpool.status
```

You should see there is one pending transaction. 

14. Now for this lab, we will again start mining using node1’s geth console with the first account which was created as sealer. When the transaction is mined we again can call the getName method and can see the updated data is showing like below:

![App Screenshot](./_readme-image/lab-done.png)

**_Checkpoint 4: Show the result to your teacher_** 

Note: We signed/mined block using only one account which is the sealer account. However, we can convert other accounts into signer account also. Fo this we need to submit a transaction and that should be validated by other sealers. You can try doing it at your home if you want. For this, more than half + 1 sealers of the network must vote for it. To do this each sealer/signer can utilise the command Provided below. However, this is not for today’s task.

```shell
clique.propose("Address of the new signer candidate", true)
```

Finally, Congratulations! you have successfully completed interaction with the smart contract that concludes our today’s lab.
