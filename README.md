## Installation of the bloXroute Gateway and Blockchain-Logger 

This document describes installation of the bloXroute Gateway and Blockchain-Logger for the bloXroute Miner Test. For this test, it is expected that you are running two BitcoinCash full nodes connected to the testnet. The bloXroute Gateway is to be connected to the first full node, while the Blockchain-Logger is to be connected to the second full node. 

The bloXroute Gateway is an application that connects your blockchain full node to the bloXroute block distribution network. Using the Gateway, the block distribution network is designed to propagate blocks to your full node faster than the traditional peer-to-peer blockchain network. It is recommended that you install the Gateway on the same server as your full node, or on a server with low latency to your full node. 

The Blockchain-Logger is a small piece of software created by bloXroute to record block propagation statistics across p2p blockchain networks. It does not provide any block propagation performance improvements and permits us to measure block propagation without the bloXroute network. 

This document describes two ways that you may install the bloXroute Gateway and the Blockchain-Logger, using either packaged docker images, or via a github download. In future versions of the Gateway and the Blockchain-Logger we expect to provide pre-packaged 'pip install' options. 


## bloXroute Gateway

### Installing the bloXroute Gateway from Docker

This section describes the installation of the bloXroute Gateway in a pre-packaged Docker form. This permits an easy, quick installation. 

This section assumes that you have Docker installed. If you would like to install Docker to use the bloXroute gateway, please visit: https://docs.docker.com/install/  There you can find installation notes for the common versions of Linux, MacOS, etc. 

Currently, we are only distributing our docker image from a private Docker Hub repository. (In the future, we expect to make our docker image public.) Accordingly, to access the docker image you will need a Docker Hub account.

1. Create username/password on Docker Hub (https://hub.docker.com/).

2. Provide bloXroute with username or email address for the account. We will add you to our team so you can access the docker image. 

3. Docker login

    ```
    docker login --username [username]
    ```
	
4. Pull gateway image

   ```
   docker pull bloxroute/bxgateway:latest
   ```
	
5. Run the docker as a daemon. "Gateway Parameters" as described below may be passed in the run command to provide the IP address and port of the full node, etc. If using the "external-port" parameter, please update "-p 9001:9001" to "-p (new port):(new port)".
   ```
   docker run -d -p 9001:9001 bloxroute/bxgateway:latest --blockchain-protocol BitcoinCash --blockchain-network Testnet
   ```

**Gateway Parameters**

| Parameter | Description | Example |
| --------- | ----------- | -------------|
| blockchain-protocol | Blockchain protocol gateway is connecting to. This test is only on the BitcoinCash network. | BitcoinCash |
| blockchain-network | Blockchain network gateway is connecting to (Mainnet or Testnet). This test is only on the Testnet. | Testnet |
| external-port | External port is the port on which the gateway can receive external connections from other gateways in the network. The default is 9001. | 9001 |
| blockchain-ip | The IP address of your BitcoinCash full node. Default is "127.0.0.1".  | "127.0.0.1" |
| blockchain-port | The port of your BitcoinCash full node. Default is 8333.  | 8333 |




### Installing the bloXroute Gateway From Github

This tutorial assumes that you are installing the Gateway from Github onto linux and will be connecting to the BitcoinCash daemon on the localhost. These instructions assume that the machine you are installing on already has installations of python 3, git, and pip. Additionally, this tutorial assumes that the RPC ports for the full node are open to receive RPC commands. 

1. Please ensure that the needed ports are open on your firewall. The bloXroute Gateway requires ports 9001 be open. 
2. Make sure the basic requirements are installed. For example, you may check for the basic requirements using the 'which' command.
    ```
    user@host$ which python3
    /usr/local/bin/python3
    
    user@host$ which git
    /usr/local/bin/git
    
    user@host$ which pip
    /usr/local/bin/pip
    ```
    If a requirement is present, the directory location is returned, as shown above. If the requirement is not present, no location is returned. 
    
3. Create a project directory
    ```
    mkdir gateway
    cd gateway
    ```
    
4. Setup a python environment for the installation 
    ```
    python3 -m venv .bxgateway
    source ./.bxgateway/bin/activate
    ```

5. Clone the repositories (Note: These are currently private repos but will be opened close to launch of the test.)
    ```
    git clone https://github.com/bloXroute-Labs/bxgateway.git
    git clone https://github.com/bloXroute-Labs/bxcommon.git
    ```

7. Enter the bxgateway directory
    ```
    cd ./bxgateway/
    ```

8. Install the bxgateway project dependencies
    ```
    pip install -r requirements.txt
    ```

9. Enter the bxcommon directory
    ```
    cd ../bxcommon
	```

8. Install the bxgateway project dependencies
    ```
    pip install -r requirements.txt
    ```    
    If the installation is successfull, you should see output similar to:
    ```
    Successfully installed asn1crypto-0.24.0 astroid-1.6.5 bitcoin-1.1.42 certifi-2019.3.9 cffi-1.12.2 chardet-3.0.4 coincurve-11.0.0 coverage-4.3.4 idna-2.7 ipaddress-1.0.22 isort-4.3.16 lazy-object-proxy-1.3.1 mccabe-0.6.1 mock-2.0.0 pbr-5.1.3 pycparser-2.19 pycryptodome-3.6.4 pyelliptic-1.5.7 pylint-1.9.2 pympler-0.5 requests-2.19.1 rlp-0.6.0 six-1.12.0 urllib3-1.23 wrapt-1.11.1
    ```
    
9. Return to parent directory
    ```	
    cd ..
	```
	
9. Launch the gateway
    1. Ensure the python environment is activated
        ```
        source ./.bxgateway/bin/activate
        ```
    2. Add bxcommon to the python path
        ```
        export PYTHONPATH=$PYTHONPATH:`pwd`/bxcommon/src
        export PYTHONPATH=$PYTHONPATH:`pwd`/bxgateway/src
        ```
    3. Run the gateway. The parameters are described further below. 
        ```
        python bxgateway/src/bxgateway/main.py --blockchain-protocol BitcoinCash --blockchain-network Testnet 
        ```
        
        It is recommended that gateway is run using the 'nohup' or 'screen' commands to permit the gateway to persist even if the terminal is closed. For example,
        ```
        nohup python bxgateway/src/bxgateway/main.py --blockchain-protocol BitcoinCash --blockchain-network Testnet &
        ```

## Blockchain-Logger

### Installing the Blockchain-Logger From Docker

This section describes the installation of the bloXroute Gateway in a pre-packaged Docker form. This section assumes you have Docker installed and that you have created an account with Docker Hub and provided the account name or email address to bloXroute. 

1. Docker login

    ```
    docker login --username [username]
    ```
	
2. Pull gateway image

   ```
   docker pull bloxroute/blockchain-logger:latest
   ```
 
3. Create a file called logger.env with environment variables as shown below. Please fill in the requested information shown in brackets. See the table below for a description of each variable. A worked example is also shown below. BLOCKCHAIN_LOG is the log file of the Bitcoin Cash node. It can often be found in the BitcoinCash data directory along with the wallet.dat files and blockchain data. For our test, NETWORK, NETWORK_NAME, and NTP_TIME are all fixed values as shown below. The RPCUSER and RPCPASS may be found in the bitcoin.conf file also in the BitcoinCash data directory. For DNS_NAME, please use the hostname of your full node, or use the following format your_organization.server_number.
    ```
    NETWORK='btc'
    RPCUSER='[RPC USER NAME]'
    RPCPASS='[RPC PASSWORD]'
    RPCIP='[IP address of BitcoinCash full node]'
    RPCPORT='[BitcoinCash full node RPC port]'
    NETWORK_NAME='BCH_TESTNET'
    DNS_NAME=[hostname of the machine]
    BLOCKCHAIN_LOG=[log location]
    NTP_TIME=1
    ```

    Complete example (Note: the RPCIP is the IP of the host machine from the perspective of docker. You should not have to change it):
    ```
    NETWORK='btc'
    RPCUSER='cKavQG4zBjI2gY1V'
    RPCPASS='l4YYz3DhKrzIyPAd'
    RPCIP='172.17.0.1'
    RPCPORT='8332'
    NETWORK_NAME='BCH_TESTNET'
    DNS_NAME='bch.testnet1.bxrtest.com'
    BLOCKCHAIN_LOG=/var/bitcoin/bitcoin_debug.log
    NTP_TIME=1 
  

4. Run the docker as a daemon. Change "/path/to/your/bitcoin" to point to your bitcoin log directory.
    ```
    docker run -d bloxroute/blockchain-logger:latest -v /path/to/your/bitcoin/bitcoin_debug.log:/var/bitcoin/bitcoin_debug.log --env-file logger.env
    ```

**Environmental Variables**
The following are environment variables that must be set for the logger to access client data.
Any variables that do not have a default value **must** be set.

| Environment Variables | Description | Default |
| --------- | ----------- | -------------|
| NETWORK | Blockchain network type - for this test 'btc' is used | btc |
| RPCUSER | If applicable, username to authenticate with client RPC API | rpcuser1 |
| RPCPASS | If applicable, password to authenticate with client RPC API | rpcpass1 |
| RPCIP | IP address the full node is listening for RPCs on | 127.0.0.1 |
| RPCPORT | Port the full node is listening for RPCs on | 9332 |
| NETWORK_NAME | Name of the network, this is fixed for the test  |'BCH_TESTNET' |
| DNS_NAME | DNS name of the full node, acts as an identifier in the reporting | **None** |
| BLOCKCHAIN_LOG | Location of the blockchain client's logs | **None** |
| NTP_TIME | Whether or not to use NTP time, 1 for ntp time | 1 |




### Installing the Blockchain-Logger From Github 

The Blockchain-Logger collects logs from your BitcoinCash full node and provides them to bloXroute. This installation assumes that you are installing the Blockchain-Logger on a server with a running full node. Currently this requires Python 2.7, we are working to upgrade the logger to Python 3. 

1. Clone the repository
   ```
   git clone https://github.com/bloXroute-Labs/blockchain-logger
   ```
   
2. Setup a python environment for the installation
    ```
    cd blockchain-logger
    virtualenv logger --python=python2.7
    source ./logger/bin/activate
    ```

   
3. Install prerequisites
    ```
    pip install psutil python-bitcoinrpc
    ```

4. Set the environment variables. See the Environmental Variables table above for a description of each variable.  BLOCKCHAIN_LOG is the log file of the Bitcoin Cash node. It can often be found in the BitcoinCash data directory along with the wallet.dat files and blockchain data. For our test, NETWORK, NETWORK_NAME, and NTP_TIME are all fixed values as shown below. The RPCUSER and RPCPASS may be found in the bitcoin.conf file also in the BitcoinCash data directory. For DNS_NAME, please use the hostname of your full node, or use the following format your_organization.server_number.
    ```
    export NETWORK='btc'
    export RPCUSER='[RPC USER NAME]'
    export RPCPASS='[RPC PASSWORD]'
    export RPCIP='[IP address of BitcoinCash full node]'
    export RPCPORT='[BitcoinCash full node RPC port]'
    export NETWORK_NAME='BCH_TESTNET'
    export DNS_NAME=[hostname of the machine]
    export BLOCKCHAIN_LOG=[log location]
    export NTP_TIME=1
    ```

    Complete example:
    ```
    export NETWORK='btc'
    export RPCUSER='cKavQG4zBjI2gY1V'
    export RPCPASS='l4YYz3DhKrzIyPAd'
    export RPCIP='127.0.0.1'
    export RPCPORT='8332'
    export NETWORK_NAME='BCH_TESTNET'
    export DNS_NAME='bch.testnet1.bxrtest.com'
    export BLOCKCHAIN_LOG=/var/bitcoin/bitcoin_debug.log
    export NTP_TIME=1
     ```
5. Run the Blockchain-Logger
    ```
    python run_logger.py
    ```
    It is recommended that Blockchain-Logger is run using the 'nohup' or 'screen' commands to permit the gateway to persist even if the terminal is closed. For example,
    ```
    nohup python run_logger.py &
    ```
    
    


