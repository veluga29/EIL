# NFT overview

## NFT란?

Non-Fungible Token의 약자로 다른 것과 대체 불가능한 토큰을 뜻합니다. 예를 들어, 돈의 경우 내가 가진 1000원은 다른 사람이 가진 1000원과 동일한 가치를 지니므로 대체 가능합니다. 반면, 나의 강아지와 다른 사람의 강아지는 동일한 경제적, 정서적 가치를 가지고 대체될 수 없습니다. 즉, NFT는 디지털 컨텐츠의 **고유성 및 원본**임을 증명해주는데 주 목적을 둡니다.

또한, NFT는 블록체인에 저장되어 있어서 누가 언제 해당 토큰을 소유했는지 전부 기록됩니다. 따라서, 이전에는 불가능했던 **디지털 자산의 소유권을 입증**하는 것도 가능해집니다.

​    

## NFT의 특징

* NFT를 사는 것은 컨텐츠를 사는 것이 아니라 컨텐츠로 연결된 데이터를 사는 것입니다.
* NFT는 소유자가 아니더라도 누구나 열람 가능합니다.
* NFT는 소유자가 아니더라도 누구나 저장할 수 있습니다.
* 구매한 NFT는 재판매할 수 있습니다.

​    

## NFT의 장점과 단점

### Pros

- 자신의 명망과 취향을 자랑하고 싶은 사람들의 Ego를 충족시킬 수 있습니다.
  - 트위터 역시 NFT를 자랑할 수 있는 탭을 만들 예정입니다.
  - 프로필 이미지도 NFT로 만들고 블록체인 검증 체크 마크를 보여줘서, 절대 유일한 프로필 사진을 가질 수 있게 해줄 계획입니다.
- Provenance(프로비넌스)
  - 해당 예술작품을 소장한 오너들의 기록을 말합니다.
  - 명망있는 사람이 소유한 적이 있다면, 그 사실 자체가 예술작품의 가치 상승에 반영되기도 합니다.
  - NFT 덕분에 소유자의 기록이 모두 기록되어 누구나 확인이 가능합니다.

### Cons

- 가치가 크게 변동되는 시장으로 인해, 투기꾼들이 몰리다보니 이미지 자체도 하락합니다.
- 막대한 전기를 소모하여 환경 오염을 촉발시킵니다.

​    

## NFT에 Contents를 삽입하는 방법

### NFT 토큰을 만드는 큰 그림

* 두 가지 기능을 가진 Smart contract 만들기

  - 돈을 받는 기능 (이더나 달러를 받으면)

  - 토큰을 전송하는 기능 (1개의 토큰을 줄게)

* 해당 토큰은 1개의 유일한 토큰이 됩니다. 그리고 1개의 유일한 토큰에 이미지, 영상, 전세 계약 등을 심으면 NFT가 됩니다.

### 용어

- ERC721: NFT의 스탠다드. ERC 20에 토큰 ID, 메타데이터 JSON 파일이 추가된 형태
- 토큰 ID: NFT에 붙는 개별 식별 번호
- 메타데이터 JSON: NFT에 넣을 정보 및 컨텐츠가 담기는 그릇
- IPFS: 위변조가 불가능한 어찌보면 블록체인과 비슷한 분산 저장소 (모든 정보를 블록체인에 담기는 비싸므로 IPFS를 항상 함께 사용함)

### 실제 과정

1. 원하는 컨텐츠(이미지, 영상 등)을 IPFS 올리고 hash 값을 받습니다.
2. 메타데이터 JSON에 hash 값을 삽입합니다.
3. 해당 메타데이터 JSON을 IPFS에 올리고 hash 값을 받습니다.
4. ERC721 민터 코드를 디플로이합니다. (코인을 내고 블록체인에 올립니다)
5. NFT를 민트합니다. (코인을 내고 NFT를 발행합니다, json hash값 주소와 wallet 주소도 적용)

​    

## NFT 전송 과정

* NFT를 manually하게 구매 및 판매할 때

  - 지갑을 설치합니다. (Ethereum 기준으로 MetaMask wallet을 설치합니다.)

  - MetaMask Mobile app을 엽니다. (NFT 거래는 아직 모바일에서만 가능합니다.)

  - NFT 탭에서 보내길 원하는 NFT를 고릅니다.

  - 상대방의 Ethereum 주소를 입력합니다.

  - Transaction (gas) fee를 지불합니다.

  - Etherscan을 통해 Ethereum 블록체인에서 해당 transaction을 등록합니다.

* NFT를 manually하게 구매할 때

  * 판매자에게 MetaMask wallet 주소를 보냅니다.

  * 판매자가 NFT를 전송했다면, transaction ID 전달도 함께 요청합니다.

  * Etherscan과 transaction ID를 통해 해당 transaction이 정상적으로 확인되면, 정식적으로 구매한 NFT의 소유주가 됩니다.

* 다만, NFT 마켓플레이스를 이용하는 것이 보다 안전하게 구매하는 방법일 것입니다. 
  * Opensea에서 판매자는 입력폼에 컨텐츠를 첨부하고 가격을 정한 후, 트랜잭션 수수료를 내면 해당 컨텐츠를 NFT로 만들어 판매할 수 있습니다.
  * OpenSea에서 구매자는 원하는 NFT를 골라 MetaMask로 NFT 가격과 gas fee 지불하면 해당 NFT를 구매할 수 있습니다.

​    

## Reference

[How to Transfer an NFT: Step by Step Guide to Do it Right](https://www.nftgators.com/how-to-transfer-an-nft/)

[NFT 광풍? 혁신일까, 마케팅일까? 개발자가 정리해드림.](https://www.youtube.com/watch?v=OfYhbX8mCE4&list=PL7jH19IHhOLOJfXeVqjtiawzNQLxOgTdq&index=14&ab_channel=%EB%85%B8%EB%A7%88%EB%93%9C%EC%BD%94%EB%8D%94NomadCoders)