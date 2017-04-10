# RegisterStepByStep

[id1]: http://sunnylee945.wixsite.com/leeyun/single-post/2017/02/21/Firebase%EF%BC%9A%E7%94%A8-Swift-%E5%BB%BA%E7%AB%8B%E3%80%8C%E8%A8%BB%E5%86%8A%E3%80%8D%E7%B3%BB%E7%B5%B1
[id2]: https://github.com/sunnyleeyun/RegisterStepByStepFinal/archive/master.zip


#### Demo ####
[![Demo](https://i.ytimg.com/vi/7EtMUehTNto/1.jpg?time=1486976666492)](https://www.youtube.com/watch?v=7EtMUehTNto)



### 註：Firebase與App的連結，以及ViewControllers的說明，請至[Firebase : 用Swift建立註冊系統][id1]，有詳盡步驟，以下只有單純就程式碼進行解釋喔！ ###






---------------------------------------


## LogInViewController ##

```
import UIKit
import Firebase
import FirebaseAuth // 這邊的Auth，是指Authentication，「新增使用者UID」或是「從Auth獲取使用者UID」需要用到這個部分
import FirebaseDatabase // 需要用到Database


class LogInViewController: UIViewController {
    
    
    @IBOutlet weak var Email: UITextField!
    @IBOutlet weak var Password: UITextField!
    
    // uid 是「使用者的獨特編碼」，在這邊儲存成一個 "" ，沒有任何值的 String，這樣聽起來有點饒舌
    // 比如說：某使用者UID是 "Avdfu12ejsiod9"<隨便亂取>，在 SignUp_Button_Tapped 或 LogIn_Button_Tapped 
    // 有一串程式碼是 「self.uid = user.uid」
    // 前者 uid 即是指 「var uid = ""」的 uid，而後者的 uid 是指 「Firebase - Auth 的 使用者UID」
    // 意思就是「將Firebase使用者uid儲存愛變數uid中」，因為 var 代表「變數」，最終就變成 var uid = "Avdfu12ejsiod9"
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
        
        
            // 接下來，FIRAuth.auth().createUser，這邊就是「新增使用者」，
            // 部落格中在步驟三的前半段，有先啟用電子郵件/密碼，所以這邊會有 withEmail, password
            // 另外提醒，順著打程式碼會出現
            // FIRAuth.auth()?.createUser(withEmail: String, password: String, completion: FIRAuthResultCallback?)，
            // 那怎麼變成 completion: { (user, error) in 這樣的呢？
            // 其實很簡單，只要在藍藍的 FIRAuthResultCallback? 按個 enter(return) 鍵，就會變成這樣囉
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
                    
                    // Online-Status 是線上狀態，在點選「登入」按鈕後，將Online-Status設定為On
                    FIRDatabase.database().reference(withPath: "Online-Status/\(self.uid)").setValue("ON")
                    
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
        // snapshot只是一個代稱(習慣為snapshot)，通常搭配.value，是指「這串路徑下的值」
        ref.observe(.value, with: { (snapshot) in  
            let name = snapshot.value as! String  // 假設 name 是這串路徑下的值，
                                                  // as! String 是因為下一行程式碼self.name_check.text為label，因此必須為String
            self.name_check.text = name  // self.name_check.text這個label為上一行程式碼所假設的 name
            self.name_check.isHidden = false  // 再把原本隱藏的顯示出來
        })
        
        
        // 下面就都一樣的意思，只是換成Gender、Email、Phone
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
        
        logOut.isHidden = false // 登出Button顯示
        changePersonalInfo.isHidden = false // 修改個人資料Button顯示
        

    }
    
    // 前往 ChangeDataViewController，一樣使用程式碼前往以避免Firebase延遲問題
    @IBAction func changePersonInfo(_ sender: Any) {
        
        let storyboard = UIStoryboard(name: "Main", bundle: nil)
        let nextVC = storyboard.instantiateViewController(withIdentifier: "ChangeDataViewControllerID")as! ChangeDataViewController
        self.present(nextVC,animated:true,completion:nil)
        
    }
    
    // 前往 LogInViewController，先在Firebase中登出，並返回到最一開始的頁面
    @IBAction func logOut(_ sender: Any) {
        
        var ref = FIRDatabase.database().reference(withPath: "Online-Status/\(uid)")
        // Database 的 Online-Status: "OFF"
        ref.setValue("OFF")
        // Authentication 也 SignOut
        try!FIRAuth.auth()?.signOut()
        
        // 前往LogIn頁面
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
    
    
    // LogInViewController 有詳細說明 uid ，這邊就不再重複了
    var uid = ""
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        // 這裡即是 uid 所解釋的，將Firebase使用者uid儲存愛變數uid裡面，在viewDidLoad中取用一次，便可在這個viewController隨意使用
        if let user = FIRAuth.auth()?.currentUser {            
            uid = user.uid
        }
        
        // 設立變數，把路徑儲存在var
        var ref: FIRDatabaseReference!
        
        // 接下來這些與 ConfirmViewController 裡面的 viewDetail 一樣，從Firebase拿取資料，這次是在viewDidLoad先做這個動作
        // 也就是在這個頁面還未跑起來時就已經從Firebase抓取資料，並顯示在 Text Field 裡面，但方式是一模一樣的
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
    
    
    // 前往ConfirmViewController，先儲存資料到Firebase，再行前往ConfirmVC檢視資料是不是即時改變了
    @IBAction func save(_ sender: Any) {
        
        // 首先確認所有 Text Field 裡面都有東西，再來就是老方法，setValue到Firebase就好囉！
        if name.text != "" && gender.text != "" && email.text != "" && phone.text != "" {
            FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Name").setValue(name.text)
            FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Gender").setValue(gender.text)
            FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Email").setValue(email.text)
            FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Phone").setValue(phone.text)
            
            // 前往ConfirmViewController
            let storyboard = UIStoryboard(name: "Main", bundle: nil)
            let nextVC = storyboard.instantiateViewController(withIdentifier: "ConfirmViewControllerID")as! ConfirmViewController
            self.present(nextVC,animated:true,completion:nil)
        }
        
    }

    
    
}
```
---------------------------------------


## 按照步驟，就可以製作出一個連接Firebase註冊系統的App！ ##

### 如果需要完整版 ( 無任何備註 ) ，請[點此][id2]開始下載。

### 最後，我們回到[部落格][id1]做個總結，看看我們是不是都學會了！
