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
![signup](https://cloud.githubusercontent.com/assets/20850892/23013399/5e9c7db8-f465-11e6-8fb2-db31650515a2.png


[id3]:https://youtu.be/KiMFQju_L4g
[id4]:https://youtu.be/5ns3Xf3aVik
1. 信箱 & 密碼 Text Field：不能為空值，才能按下註冊或登入。
⋅⋅* 按下註冊按鈕的同時，在Firebase-Authentication上成立一個新的使用者，同時在Firebase-Database中以Safety-Check: "ON"為 value新增此使用者的UID（Ｑ＆Ａ有說明何為UID），[影片解釋][id3]，這也是我們前面所說要學會的三件事中的第一件事。
⋅⋅* 按下登入按鈕的同時，Firebase會確認這個帳號與密碼是否正確，若確認無誤，才會做下一個動作，前往下一頁。

2. 註冊 Button：前往註冊頁面，利用「程式碼」前往 SignUpViewController。

3. 姓名、性別、信箱、電話 Text Field：不得為空值，才能按下確認按鈕，按下確認按鈕的同時，將姓名、性別、信箱、電話的資訊儲存至Firebase。
⋅⋅* 這個步驟，讓App裡面的資訊，成功放置到 Firebase-Database 中，如[影片][id4]，這是一個很重要功能，也是前面所說的第二件必學。

4. 確認 Button：前往登入頁面，利用「程式碼」前往 LogInViewController。

5. 登入 Button：先輸入(1)的信箱、密碼，按下「登入」按鈕前往確認頁面，利用「程式碼」前往 ConfirmViewController。





![confirm](https://cloud.githubusercontent.com/assets/20850892/23013400/6059c246-f465-11e6-8027-2bb3d2bfe5cd.png)
![changedata](https://cloud.githubusercontent.com/assets/20850892/23013401/61a95f6c-f465-11e6-8b1f-ecc84dc1b53a.png)

6. 檢視註冊資料 Button：到了確認頁面，看到最上面的 Success 圖樣，就代表登入成功了！原本虛線範圍是隱藏的，按下按鈕就會出現先前的註冊資料。

7. 姓名、性別、信箱、電話 Label：這裡就會顯現先前的註冊資料。
⋅⋅* 這是第三個重要的功能，從 Firebase 中拿取資料！

8. 修改個人資料 Button：前往更改資料頁面，利用「程式碼」前往 ChangeDataViewController。

9. 




## Ｑ＆Ａ ##

Ｑ：為什麼要由程式碼前往別的頁面，而不是直接在 MainStoryBoard 拉 Segue 就好了呢？

Ａ：因為Firebase會延遲，按下確認鍵，將註冊的個人資料丟到Firebase上，這是需要幾秒鐘的時間，倘若直接Segue，資料還來不及送達Firebase，就已經到達下一個頁面，這就非常有可能造成Error了，所以提醒大家需要多多注意Firebase的延遲問題！


Ｑ：什麼是uid呢？

Ａ：uid 是類似「身分證字號」或「獨特編碼」，就像每個人都有屬於自己的指紋，每個帳號都有屬於自己的編號，可以在Firebase-Authentication中的使用者UID看到每個帳號獨特的UID。


## LogInViewController ##

```
import UIKiimport Firebase
import FirebaseAuth

class LogInViewController: UIViewController {
    
    
    @IBOutlet weak var Email: UITextField!
    @IBOutlet weak var Password: UITextField!
    
    var uid = ""
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
    }
    
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    
    @IBAction func SignUp_Button_Tapped(_ sender: Any) {
        if self.Email.text != "" || self.Password.text != ""{
            FIRAuth.auth()?.createUser(withEmail: self.Email.text!, password: self.Password.text!, completion: { (user, error) in
                
                if error == nil {
                    if let user = FIRAuth.auth()?.currentUser{
                        self.uid = user.uid
                    }
                    
                    FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Safety-Check").setValue("ON")
                    
                    //跳到註冊頁
                    let storyboard = UIStoryboard(name: "Main", bundle: nil)
                    let nextVC = storyboard.instantiateViewController(withIdentifier: "SignUpViewControllerID")as! SignUpViewController
                    self.present(nextVC,animated:true,completion:nil)
                    
                }
                
            })
        }
    }
    
    @IBAction func LogIn_Button_Tapped(_ sender: Any) {
        if self.Email.text != "" || self.Password.text != ""{
            FIRAuth.auth()?.signIn(withEmail: self.Email.text!, password: self.Password.text!, completion: { (user, error) in
                
                if error == nil {
                    if let user = FIRAuth.auth()?.currentUser{
                        self.uid = user.uid
                        
                        FIRDatabase.database().reference(withPath: "Online-Status/\(self.uid)").setValue("ON")
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
    
    var uid = ""
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        if let user = FIRAuth.auth()?.currentUser{
            uid = user.uid
        }
        
    }
    
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    @IBOutlet weak var Confirm_Button_Tapped: UIButton!
    
    @IBAction func Confirm_Button_Tapped(_ sender: Any) {
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

    
    @IBOutlet weak var name_check: UILabel!
    @IBOutlet weak var gender_check: UILabel!
    @IBOutlet weak var email_check: UILabel!
    @IBOutlet weak var phone_check: UILabel!
    
    @IBOutlet weak var nameL: UILabel!
    @IBOutlet weak var genderL: UILabel!
    @IBOutlet weak var emailL: UILabel!
    @IBOutlet weak var phoneL: UILabel!
    
    @IBOutlet weak var logOut: UIButton!
    
    var uid = ""
    
    override func viewDidLoad() {
        super.viewDidLoad()
        if let user = FIRAuth.auth()?.currentUser {
            uid = user.uid
        }
    }
    
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    @IBOutlet weak var changePersonalInfo: UIButton!
    
    @IBAction func viewDetail(_ sender: Any) {
        
        var ref: FIRDatabaseReference!
        
        nameL.isHidden = false
        genderL.isHidden = false
        emailL.isHidden = false
        phoneL.isHidden = false
        
        
        ref = FIRDatabase.database().reference(withPath: "ID/\(self.uid)/Profile/Name")
        ref.observe(.value, with: { (snapshot) in
            let name = snapshot.value as! String
            self.name_check.text = name
            self.name_check.isHidden = false
        })
        
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
        
        logOut.isHidden = false
        changePersonalInfo.isHidden = false
        

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
            
            // 等號前面的 uid 是指 
            
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
