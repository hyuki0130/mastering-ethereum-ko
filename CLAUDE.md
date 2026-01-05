# 이더리움북 한국어 번역 가이드

## 번역 파일 위치
- 원본: `src/chapter_*.md`
- 번역본: `src/korean/chapter_*_ko.md`

## 이미지 경로 규칙
번역 파일은 `src/korean/` 폴더에 위치하므로, 원본의 이미지 경로를 상대 경로로 수정해야 합니다.

- **원본 경로**: `images/ch1/maet_0101.png`
- **번역본 경로**: `../images/ch1/maet_0101.png`

번역 시 모든 이미지 경로 앞에 `../`를 추가해야 합니다.

## 주요 번역 용어
| 영어 | 한국어 |
|------|--------|
| smart contract | 스마트 컨트랙트 |
| ether | 이더 |
| wei | 웨이 |
| gwei | 기가웨이 (gwei) |
| gas | 가스 |
| wallet | 지갑 |
| transaction | 트랜잭션 |
| block | 블록 |
| blockchain | 블록체인 |
| proof of work (PoW) | 작업 증명 (PoW) |
| proof of stake (PoS) | 지분 증명 (PoS) |
| validator | 검증자 |
| consensus | 합의 |
| state machine | 상태 머신 |
| hard fork | 하드 포크 |
| Merkle-Patricia trie | 머클-패트리샤 트라이 |
| EVM | 이더리움 가상 머신 (EVM) |
| bytecode | 바이트코드 |
| EOA (Externally Owned Account) | 외부 소유 계정 (EOA) |
| contract account | 컨트랙트 계정 |
| Solidity | 솔리디티 |
| mnemonic | 니모닉 |
| seed phrase | 시드 문구 |
| private key | 개인 키 |
| public key | 공개 키 |
| address | 주소 |
| faucet | 수도꼭지 |
| testnet | 테스트넷 |
| mainnet | 메인넷 |
| block explorer | 블록 탐색기 |
| sharding | 샤딩 |
| Verkle trees | 버클 트리 |
| permissionless | 비허가형 |
| permissioned | 허가형 |
| DApp | DApp (탈중앙화 애플리케이션) |
| DeFi | 탈중앙화 금융 (DeFi) |
| DAO | 탈중앙화 자율 조직 (DAO) |
| NFT | NFT |
| Layer 2 (L2) | 레이어 2 (L2) |
| The Merge | 더 머지 (The Merge) |

## 번역 스타일
- 기술 용어는 한국어 번역과 함께 영어 원문을 병기 (예: 스마트 컨트랙트(smart contract))
- 코드, 명령어, 파일명은 원문 그대로 유지
- 고유명사(MetaMask, Ethereum, Solidity 등)는 한글 음차 표기 또는 원문 유지
- 자연스러운 한국어 문장 구조 사용
