# RoboSurvivor（共同製作ゲーム）

## プロジェクト概要

RoboSurvivorは4名チームで共同製作したUnityゲームです。  
タイトルの通りロボットを動かす3Dアクションゲームです。  
エネミーを無限生成するゲートを潰しながら最後にボスを撃破することを目的とした爽快なゲームです。
  
![RoboSurvivorタイトル画面](readmeImg/robo_readme_1.png) 
* 制作人数：4名
* 制作期間：4日間
* 使用エンジン：Unity Editorバージョン 6000.0.54f1
* シーンレンダー：Universal 3D
* 使用言語：C#
* 使用アセット：（いずれもUnityアセットストアから）  
Demo City By Versatile Studio (Mobile Friendly) →　バトルフィールド  
Robot Hero : PBR HP Polyart　→　プレイヤーとエネミーのモデル  
Delivery Robot　→　ボスモデル  
Slash Effects FREE　→　エフェクト：接近攻撃のブレード  
Free Quick Effects Vol. 1 →　エネミーを生産するゲートモデルや爆発・炎のエフェクト  
* Fontデータ： Noto Sans JP-Medium SDF (TMP_Font Asset)  
* そのほかの使用ツール：GitHub、SorceTree、VisualStudio、Googleドライブ(スプレッドシート)で情報共有  
  
## サンプルゲーム
  
ぜひゲームを体験してみてください！  
![Robosurvivorサンプル]()
  
## ゲームフロー
  
* タイトル  
![タイトル画面](readmeImg/robo_readme_2.png)
  
* オープニングシーン
ストーリーの説明をしながらバトルが始まるという緊張感を高めます  
![オープニング画面](readmeImg/robo_readme_3.png)
  
* ゲームステージ（操作パート）  
街中のエネミーを殲滅します！  
画面右の指示にしたがって行動をすることになります。  
まずはエネミーを無限に生成するゲートを叩き、生き残ったエネミーを殲滅するといよいよボスステージです！  
![ゲームステージ画像](readmeImg/robo_readme_4.png)
  
* ボスステージ（操作パート）
ボスステージに移行すると巨大なボスロボとのバトルになります。  
ボスは距離に応じた複数のアクションをとるので、行動を見極めながら攻撃を叩きこみます。  
BOSSのライフを0にすることができたらボス撃破！エンディングシーンへと続きます。  
![ボスステージ画像](readmeImg/robo_readme_5.png)

* エンディングシーン
エンディングでは悲しげなBGMにあわせて、ひとときの休息を得たことを告げるテキストを読み上げます。  
カメラワークが最後に天を見上げたところでタイトルに戻ります。  
![エンディング画像](readmeImg/robo_readme_6.png)
  
## 操作方法
  
前後・左右　移動：WASDキー、または矢印キー  
ジャンプ：スペースキー  
プレイヤーの視点：マウス  
ショット攻撃：マウス左クリック　※弾数制限あるので打ちすぎ注意  
接近攻撃（ブレード）：マウス右  
  
プレイヤーにもライフがあり、ライフ0でゲームオーバーとなるので注意！  
![UI説明](readmeImg/robo_readme_7.png)
  
## 共同製作における主なパート
  
共同製作では主にエネミーの制御を担当しました。  
エネミーオブジェクトに対してEnemyController.csスクリプトを構築、臨場感のあるバトルを目指しました。  
  
### プレイヤーを追従するエネミー
  
エネミーは距離に応じてプレイヤーを追従するスピードを変え、難易度を調整しました。
  
* 追従しない（遠距離）
* 追従スピード速い（中距離）
* 追従スピード遅い（近距離）
* 追従しない（至近距離）  
navMeshAgent機能を使ってプレイヤーを追従  
detectionRange内で追従開始  
attackRange内で減速    
stopRange内で追従停止
```C#
 float distance = Vector3.Distance(transform.position, player.transform.position);
        if (distance < detectionRange)
        {
            //もしもプレイヤーにある程度近づいたら、近づく速度を緩めてプレイヤーに向かってShot
            if (distance < attackRange)
            {
                navMeshAgent.speed = enemySlowSpeed; //減速させる
                navMeshAgent.isStopped = false;
                navMeshAgent.SetDestination(player.transform.position);

                if (lockOn)
                {
                    // プレイヤーの高さ（Y軸）を無視して、水平に向く
                    Vector3 targetPosition = new Vector3(
                        player.transform.position.x,
                        transform.position.y, // 自分の高さを維持
                        player.transform.position.z
                        );
                    transform.LookAt(targetPosition);
                }

                //タイマー加算
                timer += Time.deltaTime;

                if (timer > shootInterval)
                {
                    Shot();
                    timer = 0f; //タイマーリセット
                }

                //近接限界距離になったらEnemyを止める
                if (distance < stopRange)
                {
                    navMeshAgent.isStopped = true; //Enemyを止める
                }
            }
            else
            {
                navMeshAgent.speed = enemySpeed; //元の速度に戻す
                navMeshAgent.isStopped = false; //Enemyを動かす
                navMeshAgent.SetDestination(player.transform.position); //playerを目的地とする
            }
        }
        else
        {
            navMeshAgent.isStopped = true; //Enemyを止める
        }
```   
  
## 共同開発におけるレビューを行いブラッシュアップ
まずは最初の2日間でプロトタイプを完成させるために担当箇所を構築しました。  
それぞれの担当箇所をGitHubを活用してマージし、当日デバッグに回れるメンバーでデバッグプレイしてプロトタイプへの評価を行いました。  
この評価に関してチームミーティングを行い、改善点と改善方法を定め残りの2日間で調整を行うことでブラッシュアップできました。
![デバッグ評価](readmeImg/team_review_a.png)
  
## GitHub上でのマージ作業を意識して担当範囲に最新の注意

スピードが要求される制作期間において、マージ作業で大きなトラブルを生まないようチームの取り決めを忠実に順守しました。  
GitHubのIssuesを通じて進捗や問題点については随時チームへの報告や問題提起を行っています。  
![issuesにおける情報共有](readmeImg/team_issues_a.png)
  
SourceTreeでブランチを分けてコンクリフト衝突がおこらないよう細心の注意を払いました。  
また定期的なコミットを通してバックアップも万全にしました。  
![SourceTreeの様子](readmeImg/team_sourcetree_a.png)
  
## 共同開発に関する工夫
  
### 仕様から反れていないかの確認作業
  
チームの打ち合わせで大体の方向性・仕様はあったものの、細かい部分は自身の考えに委ねられる環境でした。  
私の場合はとにかくユーザーが爽快に何回でもバトルしてみたくなる手応えを大事にしましたので、SEによる臨場感やシーン切り替えのタイミングなどを気にしました。  
一方でこだわった結果、チームとして想定された仕様や方向性から逸脱していないかも心配な部分でしたので、疑問に思った部分はチームリーダーにマメに確認をとり、マージする際の影響なども考えながら慎重に改良を重ねることができました。  
  
例えば、当時の心配ごととして、気軽に斬撃で勝てないよう近づきすぎるとボス本体からダメージ判定をもらうのを是としていたのですが、これが仕様上問題ないかはきちんと確認をいれました。  
  
### 細かいコミット作業
  
とにかく自分のデータにトラブルがあると、全体に影響が出てしまうので何か大きな変更を行う際にはコミットによるバージョン管理によって、いつでももとに戻れるように気を使いました。  
またコミットだけではなくプッシュを意識してクラウドにバックアップが常にある状態の維持に努めました。  
  
### 納期の意識
  
チーム開発ということで自分のせいで周りに影響がでないよう良い意味でプレッシャーを感じていたのですが、それ以上にこれをプレイするユーザーを意識して時間内に必ず間に合わせるという意識を大切にしました。そのために何日に何ができていないといけないという逆算に加え、さらに半日～1日余裕をもたせるようにスケジューリングしました。  
詰まってしまったところは、自分でこだわる部分とそうでない部分を「納期に間に合うか」で天秤にかけることで、リーダーに助力を乞うタイミングは基準を決めやすかったです。  
  
### AIの活用
  
自分で考えれる部分は多かったのですが、一部時短もかねてAIを大いに活用しました。  
しかし、AIが出したコードだけでは不十分なものも多かったので、大枠はAIの案を採用しつつも細部は自分で修正するという作業をしたことで出だしが早く無事納期に間に合わすことができたと思います。  
  
* 使用ツール：ChatGPT5
  
## 今後の課題
  
他の担当者のパートも含めゲーム全体の内容を理解はしていますが、上空を飛び交うヘリのようなNavMeshAgentコンポーネントを活用しづらいコードについては未挑戦なので、ぜひ次回以降に作ってみようと思います。  
調べたところ、いくつかの方法のうちエネミーのルーティングを行うのが一番自然だと感じましたので挑戦してみます。  
[参考サイト：Unity入門の森/移動経路の構築とOnDrawGizmosによる移動経路の可視化](https://feynman.co.jp/unityforest/game-create-lesson/tower-defense-game/enemy-route/)  
![ヘリのイメージ画像](readmeImg/future_image.png)

