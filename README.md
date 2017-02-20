# RegisterStepByStep

這是個初始範例版本，而代碼位於此README下方，

[id1]: http://sunnylee945.wixsite.com/leeyun/blog
請按照 [Firebase : 用Swift建立註冊系統][id1] 完成所有步驟，

[id2]: https://github.com/sunnyleeyun/RegisterStepByStepFinal
即可得[完整版][id2]。

---------------------------------------

This is an initial version. 

The detail source code is in this README file below.

Please follow the steps from the blog [Firebase : 用Swift建立註冊系統][id1].

Then we can get the [final version][id2].

---------------------------------------


#### Demo ####
[![Demo](https://i.ytimg.com/vi/7EtMUehTNto/1.jpg?time=1486976666492)](https://www.youtube.com/watch?v=7EtMUehTNto)

---------------------------------------

## 特定解釋 ##

Firebase與App的連結，請至[Firebase : 用Swift建立註冊系統][id1]，有詳盡步驟說明，以下只有單純就程式碼進行解釋喔！

StepByStepRegister 總共有四個 ViewController : LogInViewController、SignUpViewController、ConfirmViewController、ChangeDataViewController。



圖片中會出現三種顏色的框與解釋：


- 紅色：Text Field 或 Label，需「輸入資訊」或是「顯示訊息」。


- 藍色：Button，主要是前往其他頁面。


- 棕色：特定備註。


我們要學會三件事：

1. 在Firebase建立新帳號

2. 從App存取資料到Firebase

3. 從Firebase拿取資料到App



![login](https://cloud.githubusercontent.com/assets/20850892/23013396/5d23545c-f465-11e6-8719-762496b3a10e.png)
![signup](https://cloud.githubusercontent.com/assets/20850892/23013399/5e9c7db8-f465-11e6-8fb2-db31650515a2.png)
![confirm](https://cloud.githubusercontent.com/assets/20850892/23013400/6059c246-f465-11e6-8027-2bb3d2bfe5cd.png)
![changedata](https://cloud.githubusercontent.com/assets/20850892/23013401/61a95f6c-f465-11e6-8b1f-ecc84dc1b53a.png)

[id3]:https://youtu.be/KiMFQju_L4g
[id4]:https://youtu.be/5ns3Xf3aVik
[id5]:https://youtu.be/9goPgGhfCOg
[id6]:https://youtu.be/eqnrYgBifvY

1. 信箱 & 密碼 Text Field：不能為空值，才能按下註冊或登入按鈕。

⋅⋅* 按下註冊按鈕的同時，在Firebase-Authentication上成立一個新的使用者，同時在 Firebase-Database 中以 Safety-Check: "ON" 作為 value 新增此使用者的UID（Ｑ＆Ａ有說明何為UID），[影片解釋][id3]，這也是我們前面所說要學會的三件事中的第一件事。

⋅⋅* 按下登入按鈕的同時，Firebase會確認這個帳號與密碼是否正確，若確認無誤，才會做下一個動作，前往下一頁。

2. 註冊 Button：利用「程式碼」前往 SignUpViewController（Ｑ＆Ａ有解釋為何要用程式碼前往）。

3. 姓名、性別、信箱、電話 Text Field：不得為空值，才能按下確認按鈕，按下確認按鈕的同時，將姓名、性別、信箱、電話的資訊儲存至Firebase。
⋅⋅* 這個步驟，讓App裡面的資訊，成功放置到 Firebase-Database 中，如[影片][id4]，這是一個很重要功能，也是前面所說的第二件必學。

4. 確認 Button：利用「程式碼」前往 LogInViewController。

5. 登入 Button：先輸入(1)的信箱、密碼，按下「登入」按鈕前往確認頁面，利用「程式碼」前往 ConfirmViewController。

6. 檢視註冊資料 Button：到了確認頁面，看到最上面的 Success 圖樣，就代表登入成功了！原本虛線範圍是隱藏的，按下按鈕就會出現先前的註冊資料。

7. 姓名、性別、信箱、電話 Label：這裡就會顯現先前的註冊資料。
⋅⋅* 這是第三個重要的功能，從 Firebase 中拿取資料！

8. 修改個人資料 Button：利用「程式碼」前往 ChangeDataViewController。

9. 姓名、性別、信箱、電話 Text Field：會先在 viewDidLoad 從 Firebase 拿取資料，如[影片][id5]，其中Text Field不得為空值，編輯完成後，點選儲存並返回按鈕。

10. 確認並返回 Button：利用「程式碼」前往 ConfirmViewController，再檢視看看剛剛編輯的資料，已經即時更新了。

11. 登出 Button：如[影片][id6]，Firebase 中 Online-Status 隨著實際登入狀況改變，除此之外，Firebase 也可以像影片一樣直接改變資訊，即時更新到手機上。




## Ｑ＆Ａ ##

Ｑ：為什麼要由程式碼前往別的頁面，而不是直接在 MainStoryBoard 拉 Segue 就好了呢？

Ａ：因為Firebase會延遲，按下確認鍵，將註冊的個人資料丟到Firebase上，這是需要幾秒鐘的時間，倘若直接Segue，資料還來不及送達Firebase，就已經到達下一個頁面，這就非常有可能造成Error了，所以提醒大家需要多多注意Firebase的延遲問題！


Ｑ：什麼是uid呢？

Ａ：uid 是類似「身分證字號」或「獨特編碼」，就像每個人都有屬於自己的指紋，每個帳號都有屬於自己的編號，可以在Firebase-Authentication中的使用者UID看到每個帳號獨特的UID。


## LogInViewController ##

```
import UIKit
import Firebase
import FirebaseAuth // 這邊的Auth，是指Authentication，「新增使用者UID」或是「從Auth獲取使用者UID」需要用到這個部分
import FirebaseDatabase // 需要用到Database


class LogInViewController: UIViewController {
    
    
    @IBOutlet weak var Email: UITextField!
    @IBOutlet weak var Password: UITextField!
    
    // uid 是「使用者的獨特編碼」，在這邊儲存成一個 ""（空白的值）的 String，這樣聽起來有點饒舌，在這個地方設立變數，讓變數可以被任何function取用
    // 比如說：某使用者UID是 "Avdfu12ejsiod9"<隨便亂取>，在Fuc SignUp_Button_Tapped 或 LogIn_Button_Tapped 的 self.uid = user.uid
    // 前者 uid 即是指 「var uid = ""」的 uid，而後者的 uid 是指 「Firebase - Auth 的 使用者UID」
    // 意思就是「將 Firebase-Auth 的 使用者UID」 儲存在 「var uid」中，因為 var 代表「變數」，最終就變成 var uid = "Avdfu12ejsiod9"
    // 這樣，在我們需要使用者UID的時候（不論「從Firebase拿取資料」或是「從手機將資料放置到Firebase」皆需要用到）就可以輕易使用了！
    var uid = ""


    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
    }
    
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    // 這是前往SignUpViewController的按鈕，但在到下一個ViewController之前，先「新增了一個使用者」
    @IBAction func SignUp_Button_Tapped(_ sender: Any) {
    
        // 第一個要確保 Email & Password 這兩個 Text Field 不能什麼字都沒打，不然這樣就不合理啦！
        // 當然還能用別的假設，如：Email一定要加上"@"等等，這是最簡單，卻也不是很謹慎的方式
        // 俗話說：「一個成功的App要假設使用者的所有可能」，也就是說絕對不能讓使用者產生Bug的機會啊
        // 所以還是可以好好想想要用什麼樣的假設，但基本邏輯上這樣沒問題！
        if self.Email.text != "" || self.Password.text != ""{
        
        
            // 接下來，FIRAuth.auth().createUser，這邊就是「新增使用者」，在步驟三的前半段，有先啟用電子郵件/密碼，所以這邊會有 withEmail, password
            // 另外提醒，順著打程式碼會出現->FIRAuth.auth()?.createUser(withEmail: String, password: String, completion: FIRAuthResultCallback?)，那怎麼變成 completion: { (user, error) in 這樣的呢？其實很簡單，只要在藍藍的 FIRAuthResultCallback? 按個 enter(return) 鍵，就會變成這樣囉
            FIRAuth.auth()?.createUser(withEmail: self.Email.text!, password: self.Password.text!, completion: { (user, error) in
                
                if error == nil { 
                    if let user = FIRAuth.auth()?.currentUser{
                    
                        //這裏即是「var uid」那邊所說明的，將Firebase使用者uid儲存愛變數uid裡面，便可隨意使用，不用重複打這幾行程式碼
                        self.uid = user.uid 
                        
                }
                    
                    // 到了另一個重點啦！這是指在 database 中以 "ID/\(self.uid)/Profile/Safety-Check" 為路徑，設一個值: "ON"
                    // 要在 Database 中建立資訊，一定要 setvalue 才會成功喔！
                    FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Safety-Check").setValue("ON")
                    
                    
                    //「新增使用者」、「在Database中新增一個Safety-Check:"ON"」，接著就跳到註冊頁
                    // 另外提醒，這邊要在 Main.storyboard，Show Utilities，Identity inspector(左邊數來第三個)
                    // storyboardID 要記得改名字，像是這裡的SignUpViewController，要在storyboardID改成SignUpViewControllerID
                    let storyboard = UIStoryboard(name: "Main", bundle: nil)
                    let nextVC = storyboard.instantiateViewController(withIdentifier: "SignUpViewControllerID")as! SignUpViewController
                    self.present(nextVC,animated:true,completion:nil)
                    
                }
                
            })
        }
    }
         
    // 這是前往ConfirmViewController的按鈕，但在到下一個ViewController之前，先「在Firebase做登入的動作」
    @IBAction func LogIn_Button_Tapped(_ sender: Any) {
    
        // 一樣要先假設 Email & Password 要輸入某些字喔！
        if self.Email.text != "" || self.Password.text != ""{
            
            // 這裡跟SignUp_Button_Tapped不一樣的地方就是，從createUser改成SignIn，其餘都一樣，就不再贅述了
            FIRAuth.auth()?.signIn(withEmail: self.Email.text!, password: self.Password.text!, completion: { (user, error) in
                
                if error == nil {
                    if let user = FIRAuth.auth()?.currentUser{
                        self.uid = user.uid
                }
                    
                    FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Safety-Check").setValue("ON")
                    
                    //跳到確認頁
                    let storyboard = UIStoryboard(name: "Main", bundle: nil)
                    let nextVC = storyboard.instantiateViewController(withIdentifier: "ConfirmViewControllerID")as! ConfirmViewController
                    self.present(nextVC,animated:true,completion:nil)
                }
                
            })
        }
    }
    
}

```

---------------------------------------

## SignUpViewController ##

```
import UIKit
import Firebase
import FirebaseAuth 
import FirebaseDatabase

class SignUpViewController: UIViewController {
    
    
    @IBOutlet weak var Name: UITextField!
    @IBOutlet weak var Gender: UITextField!
    @IBOutlet weak var Email: UITextField!
    @IBOutlet weak var Phone: UITextField!
    
    // LogInViewController 有詳細說明 uid ，這邊就不再重複了
    var uid = ""
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        // 這裡即是 uid 所解釋的，將Firebase使用者uid儲存愛變數uid裡面，在viewDidLoad中取用一次，便可在這個viewController隨意使用
        if let user = FIRAuth.auth()?.currentUser{
            uid = user.uid
        }
        
    }
    
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    @IBOutlet weak var Confirm_Button_Tapped: UIButton!
    
    // 這是前往LogInViewController的按鈕，但在到下一個ViewController之前，先「確認註冊資料填寫完畢」
    @IBAction func Confirm_Button_Tapped(_ sender: Any) {
    
        // 在四個 Text Field 中都要輸入東西，接著把所有在手機上輸入的資訊，在Firebase中setvalue，就即時更新到 Firebase 了！
        if Name.text != "" && Gender.text != "" && Email.text != "" && Phone.text != ""{
            FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Name").setValue(Name.text)
            FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Gender").setValue(Gender.text)
            FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Email").setValue(Email.text)
            FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Phone").setValue(Phone.text)
            
            //跳回登入頁
            let storyboard = UIStoryboard(name: "Main", bundle: nil)
            let nextVC = storyboard.instantiateViewController(withIdentifier: "LogInViewControllerID")as! LogInViewController
            self.present(nextVC,animated:true,completion:nil)
        }
    }
    
    
    
}
```

---------------------------------------


## ConfirmViewController ##

```
import UIKit
import Firebase
import FirebaseAuth
import FirebaseDatabase

class ConfirmViewController: UIViewController {

    // 這是從Firebase拿取資訊，顯示實際註冊資料的Label
    @IBOutlet weak var name_check: UILabel!
    @IBOutlet weak var gender_check: UILabel!
    @IBOutlet weak var email_check: UILabel!
    @IBOutlet weak var phone_check: UILabel!
    
    // 以下四個是純粹的姓名、性別、信箱、電話的Label，原先設立為 isHidden，按下按鈕才顯示出來
    @IBOutlet weak var nameL: UILabel!
    @IBOutlet weak var genderL: UILabel!
    @IBOutlet weak var emailL: UILabel!
    @IBOutlet weak var phoneL: UILabel!
    
    // 登出Button，原先也是 isHidden，按下按鈕才顯示出來
    @IBOutlet weak var logOut: UIButton!
    
    // LogInViewController 有詳細說明 uid ，這邊就不再重複了
    var uid = ""
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        // 這裡即是 uid 所解釋的，將Firebase使用者uid儲存愛變數uid裡面，在viewDidLoad中取用一次，便可在這個viewController隨意使用
        if let user = FIRAuth.auth()?.currentUser {
            uid = user.uid
        }
        
    }
    
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    // 編輯個人資料Button(會前往另一個頁面)，原先也是 isHidden，按下按鈕才顯示出來
    @IBOutlet weak var changePersonalInfo: UIButton!
    
    @IBAction func viewDetail(_ sender: Any) {
        
        // 指 ref 是 firebase中的特定路徑，導引到特定位置，像是「FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Name")」
        var ref: FIRDatabaseReference!
        
        // 這只是將原先隱藏起來的label顯示出來
        nameL.isHidden = false
        genderL.isHidden = false
        emailL.isHidden = false
        phoneL.isHidden = false
        
        // 接下來也是很重要的一步，從Firebase拿取資料，並顯示為label(name, gender, email, phone)
        
        // 前面有個var ref，把這一串路徑除存在變數中
        ref = FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Name")
        
        // .observe 顧名思義就是「察看」的意思，也就是說ref.observe(.value)->查看「這串導引到特定位置的路徑」的value
        ref.observe(.value, with: { (snapshot) in  // snapshot只是一個代稱(習慣為snapshot)，通常搭配.value，是指「這串路徑下的值」
            let name = snapshot.value as! String  // 假設 name 是這串路徑下的值，為 String 是因為下一行程式碼self.name_check.text為label，因此必須為String
            self.name_check.text = name  // self.name_check.text這個label為上一行程式碼所假設的 name
            self.name_check.isHidden = false  // 再把原本隱藏的顯示出來就好囉
        })
        
        
        // 下面就都一樣囉！
        ref = FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Gender")
        ref.observe(.value, with: { (snapshot) in
            let gender = snapshot.value as! String
            self.gender_check.text = gender
            self.gender_check.isHidden = false
        })
        
        ref = FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Email")
        ref.observe(.value, with: { (snapshot) in
            let email = snapshot.value as! String
            self.email_check.text = email
            self.email_check.isHidden = false
        })
        
        ref = FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Phone")
        ref.observe(.value, with: { (snapshot) in
            let phone = snapshot.value as! String
            self.phone_check.text = phone
            self.phone_check.isHidden = false
        })
        
        logOut.isHidden = false // logOut按鈕顯示
        changePersonalInfo.isHidden = false // 
        

    }
    
    @IBAction func changePersonInfo(_ sender: Any) {
        
        let storyboard = UIStoryboard(name: "Main", bundle: nil)
        let nextVC = storyboard.instantiateViewController(withIdentifier: "ChangeDataViewControllerID")as! ChangeDataViewController
        self.present(nextVC,animated:true,completion:nil)
        
    }
    
    
    @IBAction func logOut(_ sender: Any) {
        
        var ref = FIRDatabase.database().reference(withPath: "Online-Status/\(uid)")
        ref.setValue("OFF")
        try!FIRAuth.auth()?.signOut()
        
        let storyboard = UIStoryboard(name: "Main", bundle: nil)
        let nextVC = storyboard.instantiateViewController(withIdentifier: "LogInViewControllerID")as! LogInViewController
        self.present(nextVC,animated:true,completion:nil)

        
    }
    
    
}

```

---------------------------------------


## ChangeDataViewController ##
 
```
import UIKit
import Firebase
import FirebaseAuth
import FirebaseDatabase

class ChangeDataViewController: UIViewController {
    
    @IBOutlet weak var name: UITextField!
    @IBOutlet weak var gender: UITextField!
    @IBOutlet weak var email: UITextField!
    @IBOutlet weak var phone: UITextField!
    
    
    // 先假設
    var uid = ""
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        
        if let user = FIRAuth.auth()?.currentUser {            
            uid = user.uid
        }
        
        
        var ref: FIRDatabaseReference!
        
        ref = FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Name")
        ref.observe(.value, with: { (snapshot) in
            let name = snapshot.value as! String
            self.name.text = name
        })
        
        ref = FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Gender")
        ref.observe(.value, with: { (snapshot) in
            let gender = snapshot.value as! String
            self.gender.text = gender
        })
        
        ref = FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Email")
        ref.observe(.value, with: { (snapshot) in
            let email = snapshot.value as! String
            self.email.text = email
        })
        
        ref = FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Phone")
        ref.observe(.value, with: { (snapshot) in
            let phone = snapshot.value as! String
            self.phone.text = phone
        })
        

    }
    
    
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    
    @IBAction func save(_ sender: Any) {
        
        if name.text != "" && gender.text != "" && email.text != "" && phone.text != "" {
            FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Name").setValue(name.text)
            FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Gender").setValue(gender.text)
            FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Email").setValue(email.text)
            FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Phone").setValue(phone.text)
            
            
            let storyboard = UIStoryboard(name: "Main", bundle: nil)
            let nextVC = storyboard.instantiateViewController(withIdentifier: "ConfirmViewControllerID")as! ConfirmViewController
            self.present(nextVC,animated:true,completion:nil)
        }
        
    }

    
    
}
```
