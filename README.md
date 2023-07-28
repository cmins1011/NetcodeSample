# NetcodeSample
including synchronizing samples of Player, network variables, and RPC call

## 1. HMD 연동

… 아직 진행 중 …

## 2. Synchronize

### 2.0. Install Package

1. Window > Package Manger > in Unity Registry > Netcode for GameObjects & Multiplayer Tools 설치
2. Window > Package Manger > + > Add package from git URL … > 아래 링크 설치
    
    [https://github.com/Unity-Technologies/com.unity.multiplayer.samples.coop.git?path=/Packages/com.unity.multiplayer.samples.coop#main](https://github.com/Unity-Technologies/com.unity.multiplayer.samples.coop.git?path=/Packages/com.unity.multiplayer.samples.coop#main)
    

### 2.1. Network Manager

1. 빈 GameObject인 NetworkManager 생성
2. add **NetworkManager.cs**
3. Log Level > Developer 바꾸기
4. networking할 prefabs list 모두 리스트에 넣어주어야 함.
5. add **UnityTransport.cs**
    - Protocol > Unity Transport 바꾸기

### 2.2. Player Sync

1. add **NetworkObject.cs**
2. add **ClientNetworkTransform.cs**
    - 각 clients들이 자신의 local position 변경상태를 server에 send 할 수 있게 됨
    - syncing 할 transformation 설정 (주로 position과 rotation Y)
3. **PlayerNetwork.cs** 새로 생성
    
    ```csharp
    using System.Collections;
    using System.Collections.Generic;
    using Unity.Netcode;
    using UnityEngine;
    
    public class PlayerNetwork : NetworkBehaviour // Instead of MonoBegaviour
    {
        private void Update() {
            if (!IsOwner) return;
    
            Vector3 moveDir = new Vector3(0,0,0);
    
            if (Input.GetKey(KeyCode.W)) moveDir.z = +1f;
            if (Input.GetKey(KeyCode.S)) moveDir.z = -1f;
            if (Input.GetKey(KeyCode.A)) moveDir.x = -1f;
            if (Input.GetKey(KeyCode.D)) moveDir.x = +1f;
            
            float moveSpeed = 3f;
            transform.position += moveDir * moveSpeed * Time.deltaTime; 
        }
    }
    ```
    
4. 위의 예제처럼 Player 스크립트에 `MonoBehaviour` → `NetworkBehaviour` 상속으로 바꾸기
5. Update() 에 `if (!IsOwner) return;`
    - 각 owner들만 조정할 수 있게하기 위함.
6. **NetworkManager**의 inspector창에서 Player Prefab 부분에 위의 스크립트가 모두 추가된 player 프리팹 넣어주기

### 2.3. Objects’ Position Sync

1. sync 하고 싶은 prefab에 add **NetworkObject.cs**
2. add **ClientNetworkTransform.cs**
    - syncing 할 transformation 설정
3. **NetworkManager**의 inspector창에서 Network Prefabs Lists 부분에 위의 스크립트가 모두 추가된 프리팹 넣어주기

### 2.4. Animation Sync (optional)

1. add **OwnerNetworkAnimator.cs**
    
    ```csharp
    using Unity.NetCode.Components;
    
    public class OwnerNetworkAnimator : NetworkAnimator {
    	 protected override bool OnIsServerAuthoritative() {
    		  return false;
    	 }
    }
    ```
    
2. Inspector 창에서 GameObject에 적용되어 있는 animator 넣어주기 (애니메이션 넣고 싶다면!)

---

## Reference

[COMPLETE Unity Multiplayer Tutorial (Netcode for Game Objects)](https://www.youtube.com/watch?v=3yuBOB3VrCk)

[Building your multiplayer VR experience: Making a simple Multiplayer VR app using the Unity SharedSpaces Sample](https://developers.facebook.com/blog/post/2022/06/28/building-your-multiplayer-vr-experience-making-multiplayer-vr-app/)
