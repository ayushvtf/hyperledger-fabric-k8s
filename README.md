# hyperledger-fabric-k8s
```
kubectl create ns hyperledger

kubectl create -f pv/  -n hyperledger

kubectl exec -it fabric-tools -- mkdir /fabric/config -n hyperledger
kubectl exec -it fabric-tools -- mkdir /fabric/channel-artifacts -n hyperledger
kubectl cp configtx.yaml fabric-tools:/fabric/config/  -n hyperledger
kubectl cp crypto-config.yaml fabric-tools:/fabric/config/ -n hyperledger
kubectl exec -it fabric-tools -- /bin/bash
cryptogen generate --config /fabric/config/crypto-config.yaml
cp -r crypto-config /fabric/
configtxgen -profile TwoOrgsOrdererGenesis -channelID testchainid -outputBlock /fabric/channel-artifacts/genesis.block
configtxgen -profile TwoOrgsChannel -outputCreateChannelTx /fabric/channel-artifacts/channel.tx -channelID "mychannel"
configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate /fabric/channel-artifacts/org1Anchors.tx  -channelID "mychannel" -asOrg org1MSP
configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate /fabric/channel-artifacts/org2Anchors.tx  -channelID "mychannel" -asOrg org2MSP
exit

kubectl create -f orderer-service/
kubectl create -f org1/
kubectl create -f org2/


kubectl exec -it cli-org1-d6b4cc8f9-bfb8d  -n hyperledger  -- /bin/bash 
peer channel create -o orderer0:7050 -c mychannel -f /fabric/channel-artifacts/channel.tx --tls true --cafile $ORDERER_CA
peer channel join -b mychannel.block

kubectl exec -it cli-org2-67d6457d47-dq5wh  -n hyperledger  -- /bin/bash 
peer channel fetch 0 mychannel.block -c mychannel -o orderer0:7050 --tls --cafile $ORDERER_CA
peer channel join -b mychannel.block

peer channel list
```
