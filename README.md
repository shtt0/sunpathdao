# sunpathdao  

#Programを組み込んでいるProduct概要
1.行政の道路に不備がないかの見回り活動を、一般ドラーバーがDAO的に担い、見回し時に動画をアップロード（オフチェーン対応）したユーザのWalletアドレスに活動報酬が送金されるサービス。  
2.行政が委託のタスクを作る(create_task)には、フロントでコース指定などを行った後、報酬額のSOLをタスク専用のPDA（Program Derived Address）アカウントにロックし、ユーザの動画提出を待つ。  
3.ユーザの提出があったら、オフチェーンで委託者にJudge依頼が飛び、Accept(accept_task）かReject（reject_task）を行い、Acceptならロックされていた報酬が提出者のWalletに送金（Walletの指定はフロント側で対応）される。Rejectすると、一定期間待った後に、委託者はLockしていた報酬をWithdrawできる。    
※この辺は色々ガバガバですが、Productの動きがわかれば良い精神でやっています。  
4.報酬送金の5％が、DAOトレジャリーへ手数料として徴収されます。  
5.報酬を受け取るユーザは、一緒にDAOガバナンストークンを受け取れます。  

//Programつなぎこみが終わってないですが、Productイメージは以下です。  
https://sunpathdao.replit.app/

#programコード
[rib.rs](src/rib.rs)
をsolana playgroundのAnchorフレームワーク×DevnetでBuild/Deploy.    

#programID   
```
Drr2eM6yoGXL2QZHdaFzXzUDDPQarV8acbbYWTBAtNyE  
```

#Acconuts/config publickey 
//seed(1)string:config_v2 * programID で生成済  
```
HJCLK4Bvk3QV3XekUHvN1EnSt7se42QDyLDKofk5Thow  
```
//上記PrgramConfigで初期化済

#createTaskでTaskId3のTaskを発行済。  
//以下のTaskAcoountを生成.報酬の1SOLがDepositされている.
//Playground上でFetchしてみてもここは正常な模様。
```
G644SgRDgy4Lb6vthHgYfGEtXmpWjbXEiScvDGJxu4Eq  
```
//なお、createTask input値はLog参照.  
https://explorer.solana.com/tx/HSWGRUHb9JF82etQfSdE4Ypga55DaXnQaRd4FTUHdSLEhUMxCGFmYKvU43YJsi4pUYbmtxL7f7H2VAY9sftceqA?cluster=devnet  


#次が、今エラーが出て困っているacceptTaskです。

**Logは以下が出ています。**  
```
Testing 'acceptTask'...  
❌  Test 'acceptTask' failed:   
Instruction index: 0  
Reason: Invalid program argument.  
```

//今、TESTで入力している引数ですが、

#Args/recipient(報酬受け取りユーザ）
```
CJLNF2N9UqS1DiDXqpSMTNRRH91gNkQFyXpMHHTwyeuS
```
//上のアドレスですが、はじめてWalletアドレスを持ったという想定のユーザでアドレスには何もトークンが入っていません。そのため、Account does not existのステータスです.
//これが原因かと思ったのですが、送金時にPDAが自動生成されるという趣旨のドキュメントを見ました（それがどこに書いてあったかロストしました・・・）
//また、Txを発生させたことがあるアドレスで検証しても同様の結果でした。

#Acoount/taskAccount(Id3で生成済の上記のpublickey)
```
G644SgRDgy4Lb6vthHgYfGEtXmpWjbXEiScvDGJxu4Eq
```  

#consignerWallet(タスクを発行した際のWallet）
```
G644SgRDgy4Lb6vthHgYfGEtXmpWjbXEiScvDGJxu4Eq
```

#recipientAccount(Geminiくんにからは、受取アドレスそのままで良いと言われています・・・)
```
CJLNF2N9UqS1DiDXqpSMTNRRH91gNkQFyXpMHHTwyeuS
```

#config(上の通り発行済)
```
HJCLK4Bvk3QV3XekUHvN1EnSt7se42QDyLDKofk5Thow
```  

#adminActionCounter(Accept/Denyをそれぞれした回数をオンチェーンで記録しています)
//seed(1):string `admin_counter` × seed((2)publickey `current wallet` × ProgramIdでGenerate
```
H4Y6dE94KMuzf6T84uJCiHnnHvkqwVts6ZxMpjijhK1q
```

#systemProgram
```
11111111111111111111111111111111
```
//これでTESTすると上記のエラーログになります。  
//rejectTaskの方は問題なく実行できています。  
//もしアドバイスいただけるところがあれば、お願いいたします。  
