# RegisterStepByStep

內容代碼

[![Demo](https://i.ytimg.com/vi/7EtMUehTNto/1.jpg?time=1486976666492)](https://www.youtube.com/watch?v=7EtMUehTNto)



## LogInViewController ##

```
import UIKit
import Firebase
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

＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝

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
        if Name != nil && Gender != nil && Email != nil && Phone != nil {
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

＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝


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

＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝


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
        
        if name != nil && gender != nil && email != nil && phone != nil {
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
