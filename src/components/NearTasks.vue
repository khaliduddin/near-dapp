<template>
  <div>
    <h3>NEAR TASKS</h3>
    <button @click="connectWallet">Connect Wallet</button>    
    <button @click="disconnectWallet">Disconnect Wallet</button>
    <p>
        <label>{{ walletConnectionStatus ? "connected" : "not connected" }}</label>
    </p>
  </div>
</template>

<script>
import * as nearAPI from 'near-api-js'
import { ref, onMounted } from 'vue'

export default {
    setup() {
        let walletConnectionStatus = ref(false)
        const { connect, keyStores, WalletConnection } = nearAPI
        const myKeyStore = new keyStores.BrowserLocalStorageKeyStore()
        console.log(myKeyStore)

        const connectionConfig = {
            networkId: "testnet",
            keyStore: myKeyStore, // first create a key store 
            nodeUrl: "https://rpc.testnet.near.org",
            walletUrl: "https://wallet.testnet.near.org",
            helperUrl: "https://helper.testnet.near.org",
            explorerUrl: "https://explorer.testnet.near.org",
        };

        let walletConnection;

        const initiateConnection = async () => {
            console.log('initializing connection')
            const nearConnection = await connect(connectionConfig)
            console.log('near connection ', nearConnection)

            walletConnection = new WalletConnection(nearConnection);

            console.log('process is complete')
        }

        // const nearConnection = async () => {
        //     let connection = await connect(connectionConfig)
        //     console.log('connection ', connection)
        //     return connection
        // }

        // console.log('near connection ', nearConnection())

        onMounted(() => {
            console.log('near tasks component is mounted')
            initiateConnection()        
        })

        const connectWallet = () => {
            console.log('connecting wallet')
            if(!walletConnection.isSignedIn()) {
                walletConnection.requestSignIn({
                    contractId: "gamelands.testnet",
                    methodNames: [],
                    successUrl: "",
                    failureUrl: ""
                })

                toggleWalletStatus()
            }
        }

        const disconnectWallet = () => {
            console.log('disconnecting wallet')
            if(walletConnection.isSignedIn()) {
                walletConnection.signOut()
                toggleWalletStatus()
            }
        }

        const toggleWalletStatus = () => {
            walletConnectionStatus.value = !walletConnectionStatus.value
        }

        return {
            walletConnectionStatus,
            connectWallet,
            disconnectWallet
        }
    }
}
</script>

<style>

</style>