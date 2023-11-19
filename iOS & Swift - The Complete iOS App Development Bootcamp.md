# iOS & Swift - The Complete iOS App Development Bootcamp Note

@Author: Kate Fu

@Start Date: 13/06/2022

[TOC]



## XCode

Shortcuts:

https://swifteducation.github.io/assets/pdfs/XcodeKeyboardShortcuts.pdf

command+shift+Y: hide debug area

Color helper: https://colorhunt.co/

- make my own icon:
  - create icon: https://www.canva.com/
  - app icon generator: https://appicon.co/
  - Add Images to appIcon: select screen resolution (1x, 2x, 3x)

When we want to change the image for an imageView, we need to type in `#imageLiteral(` and the Xcode will auto-complete.

### set constraints

1. add constraints

![Screen Shot 2022-06-18 at 9.24.25 PM](/Users/katefu/Library/Application Support/typora-user-images/Screen Shot 2022-06-18 at 9.24.25 PM.png)

2. add alignment constraints

   ![Screen Shot 2022-06-18 at 9.25.29 PM](/Users/katefu/Library/Application Support/typora-user-images/Screen Shot 2022-06-18 at 9.25.29 PM.png)

   3. add container (UIView)
   4. add stack view: select containers - editor - embed in - stack view

![Screen Shot 2022-06-19 at 6.51.11 PM](/Users/katefu/Library/Application Support/typora-user-images/Screen Shot 2022-06-19 at 6.51.11 PM.png)

### Audio play

```swift
import AVFoundation
var player:AVAudioPlayer?
func playSound(){
        guard let url = Bundle.main.url(forResource: "C", withExtension: "wav") else {return}
        do{
            try AVAudioSession.sharedInstance().setCategory(.playback, mode: .default)
            try AVAudioSession.sharedInstance().setActive(true)
            player = try AVAudioPlayer(contentsOf: url, fileTypeHint: AVFileType.mp3.rawValue)
            guard let player = player else {
                return
            }
            player.play()
            
        }catch let error{
            print(error.localizedDescription)
        }
    }
```

### Section 7

1. select buttom text: sender.currentTitle/ sender.titleLabel.text

2. convert option String to a function parameter: String!

3. set alpha: sender.alpha

4. delay the code

   ```swift
   DispatchQueue.main.asyncAfter(deadline: .now()+0.2){
   	sender.alpha = 1.0
   }
   ```

### Section 8

1. set label text auto wrap in small screens: set label - lines to 0 or set autoshrink - min font size to 15

2. our sight is from bottom of the outline, if you want something to be not hidden, move it down

3. Warning: Initialization of immutable value 'hardness' was never used; consider replacing with assignment to '_' or removing it: add a _

4. a...b : close range, a ..< b: half open range, ...b: one sided range

5. optional dataType: 

   ```swift
   var playerName : String? = nil
   playerName = "cdysavfdsa"
   var unwrapperPlayerName = playerName!
   ```

6. Set timer & do something after the time

   ```swift
       var counter = 60
   var timer = Timer()
       @IBAction func hardnessSelected(_ sender: UIButton) {
         timer.invalidate()//stop previous one
         timer = Timer.scheduledTimer(timeInterval: 1.0, target: self, selector: #selector(updateCounter), userInfo: nil, repeats: true)
           
       }
       @objc func updateCounter() {//objectC 
           //example functionality
           if counter > 0 {
               print("\(counter) seconds")
               counter -= 1
           }else{
             timer.invalidate()
             //do something after the timer
           }
       }
   ```

7. progressView, style: bar (can give height in constraints)

   set value: progress

8. divide float: let percentageProgress = Float(secondsPassed)/Float(totalTime)

### Section 9

1. total element in a array: array.count

2. Struct with init()

3. delay between codes

   ```swift
   Timer.scheduledTimer(timeInterval: 0.2, target: self, selector: #selector(updateUI), userInfo: nil, repeats: false)
   ```

4. struct properties(self) are immutable, mark the method as mutating

5. change button text: choice2Button.setTitle(choice2, for: .normal)

### Section 11

1. UISlider：Horizontal Slider，set min，max

2. Round a float to digits: String(format: "%.2f", sender.value), to no digits:Int(sender.value)

3. Pow(x, n)

4. class vs struct

5. create a new view controller for a new page:

   ```swift
   import UIKit //UIKit framework for IOS
   class SecondViewController: UIViewController {
       override func viewDidLoad() {
           view.backgroundColor = .red //UIColor.red, it knows
           super.viewDidLoad()
           //create UI without storyboard
           let label = UILabel()
           label.text = bmiValue
           label.frame = CGRect(x: 0, y: 0, width: 100, height: 50)
           view.addSubview(label) //UILabel inherites from UIView
       }
   }
   ```

   ```swift
   let secondVC = SecondViewController()
   secondVC.bmiValue = String(format: "%.1f", bmi)
   self.present(secondVC, animated:true, completion: nil)
   ```

6. Segues and navigation for multi-screen

   - new a cocoa-touch file in controllers

   - link the storyboard's viewcontroller - identity - customer class to the new controller we just created

   - link the views (with ^), select segue, give it an identifier

   - **self**.performSegue(withIdentifier: "goToResult", sender: **self**)

   - create a init function

     ```swift
     override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
        if segue.identifier == "goToResult"{
             let destinationVC = segue.destination as! ResultViewController //downcasting
             destinationVC.bmiValue = "0.0"
             }
         }
     ```

   - backward

     ```swift
     @IBAction func recalculatePressed(_ sender: UIButton) {
             self.dismiss(animated: true, completion: nil)
     }
     ```

![Screen Shot 2022-06-27 at 3.54.35 PM](/Users/katefu/Library/Application Support/typora-user-images/Screen Shot 2022-06-27 at 3.54.35 PM.png)

### Section 12

1. SF symbols (https://developer.apple.com/design/human-interface-guidelines/foundations/sf-symbols)

2. system color will change with dark mood (custom will not)

   to make it fit: assets - new color set - set light and dark appearance - set symbol color to named color set.

3. can also use pdf image (vector image are more clear? they don't pixelate)

   drag the pdf to asset-background, check the [preserve vector data], single scale

   Change background light/dark mood: change [appearence] to [any, light, dark], add images

4. UITextField, can set keyboards format.

   Capitalization - words

   to make the keyboard [return button] work:

   - Class ViewController : UIViewController, UITextFieldDelegate{}

   - Inside viewDidLoad: searchTextField.delegate = self

   - ```swift
     func textFieldShouldReturn(_ textField: UITextField) -> Bool {
             print(searchTextField.text!)
             return true
     }
     ```

   to make the keyboard disappear after press [return button]:

   ```swift
   searchTextField.endEditing(true)
   ```

   To make the textfield clear after clicking search/return:

   ```swift
   func textFieldDidEndEditing(_ textField: UITextField) {
           searchTextField.text = ""
   }
   ```

   To verify the text:

   ```swift
       func textFieldShouldEndEditing(_ textField: UITextField) -> Bool {
           if textField.text != ""{
               return true
           }else{
               textField.placeholder = "Type something"
               return false
           }
       }
   ```

5. delegate pattern

   ```swift
   //UITextFieldDelegate
   protocol UITextFieldDelegate{
     func textFieldDidBeginEditing()
   }
   //UITextField
   var delegate: UITextFieldDelegate
   delegate.textFieldDidBeginEditing() //will trigger automatically
   //ViewController
   let textField = UITextField() //UITextField inherits UITextFieldDelegate
   textField.delegate = self //delegate (property)=view controller (because our view controller adopts UITextField)
   func textFieldDidBeginEditing(){
     //do something
   }
   ```

   

6. openweather api: https://openweathermap.org/api

7. code block(ready to fill) click to blue and press enter to make it trailing closure

8. JSON struct need to inherit from [Decodable] protocol

9. catch error and do something

   ```swift
   do{
       try decoder.decode(WeatherData.self, from: weatherData)
   }catch{
       print(error)
   }
   ```

10. conputed property:

    ```swift
    var conditionName:String{
        switch weatherId {
              case 200...232:
              return "cloud.bolt"
              case 300...321:
              return "cloud.drizzle"
              default:
              return "cloud"
              }
        }
    ```


12. use extension to restructure code

13. use [//MARK: -] to create a comment title

14. create it as a code snippet

15. get user location data

    - import CoreLocation

    - **let** locationManager = CLLocationManager()

    - locationManager.requestWhenInUseAuthorization()

    - in info.plist, add new [Privacy - Location When in Use]

    - ```swi
      locationManager.requestWhenInUseAuthorization()
      locationManager.requestLocation()
      locationManager.delegate = self
      ```

    - in extension WeatherViewController: CLLocationManagerDelegate

      ```
      
      ```


### Section 14

1. add navigation bar: select - editor - embed in - navigator

2. Animation for text letters:

   ```swift
           var charIndex = 0.0
           for letter in titleText{
               print("-")
               print(1*charIndex)
               print(letter)
               Timer.scheduledTimer(withTimeInterval: 1*charIndex, repeats: false) { (timer) in
                   self.titleLabel.text?.append(letter)
               }
               charIndex += 1
           }
   ```

3. thirdparty library: cocoapods

   - Sudo gem install cocoapods

   - pod setup --verbose

   - cd to project folder, pod init

   - add library command into pod file, pod install

   - pod install cocoapods error:

     open terminal in Rosetta mode (get info, select checkbox, restart app)

     sudo gem install ffi -- --enable-system-libffi

     pod install

4. change the UILabel to CLTypingLabel

5. Package dependicies manager: cocoapods, earthage, swift package manager

6. use google firebase

   https://console.firebase.google.com/project/flash-chat-ios13-351e6/overview

   - register application

   - Download plist file

   - pod install: pod 'Firebase/Auth' and  pod 'Firebase/Firestore'

   - add init code in AppDelegate file: import, configure()

   - enable in Firebase console - authentication - email

   - Code in registerBtnPressed:

     ```swift
             if let email = emailTextfield.text, let password = passwordTextfield.text{
                 Auth.auth().createUser(withEmail: email, password: password){authResult, error in
                     if let e = error{
                         print(e)
                     }else{
                         //navigate to chat view controller
                         self.performSegue(withIdentifier: "RegisterToChat", sender: self)
                     }
                 }
             }
     ```

   - Code in LoginBtnPressed:

     ```
     
     ```

   - Code in logoutBtnPressed:

     ```
     
     ```

     ​	

7. Constants file

   use [static]: don't need to new a Constants instance

   ```swift
   struct Constants{
       static let registerSegue = "RegisterToChat"
       static let loginSegue = "LoginToChat"
   }
   ```

8. table view data source

   ```swift
   extension ChatViewController: UITableViewDataSource{
       func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
           let cell = tableView.dequeueReusableCell(withIdentifier: K.cellIdentifier, for: indexPath)
           cell.textLabel?.text = messages[indexPath.row].body
           return cell
       }
       
       func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
           return messages.count
       }
   }
   ```

   - Create new MessageCell file under view folder (check xlb), design and connect

   - register the MessageCell file in ChatViewController

   - change the cell type to MessageCell in ChatViewController

     ```swift
     func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
             let cell = tableView.dequeueReusableCell(withIdentifier: K.cellIdentifier, for: indexPath) as! MessageCell
             cell.label.text = messages[indexPath.row].body
             return cell
         }
     ```

   - Adjust bubble cornor radius:

     messageBubble.layer.cornerRadius = messageBubble.frame.size.height / 5

   - make label auto wrap: line = 0

9. firebase database: Cloud fireoate & realtime database - test mode - location

   - Add **let** db = Firestore.firestore() in AppDelegate

   - save data in database

     ```swift
         @IBAction func sendPressed(_ sender: UIButton) {
             if let messageBody = messageTextfield.text, let messageSender = Auth.auth().currentUser?.email{
                 db.collection(K.FStore.collectionName).addDocument(data: [
                     K.FStore.senderField:messageSender,
                     K.FStore.bodyField:messageBody
                 ]) { error in
                     if let e = error{
                         print("Issue saving data into store")
                     }else{
                         print("Successfully saved data")
                     }
                 }
             }
         }
     ```

   - retrieve data from database

     ```swift
     func loadMessages(){
             messages = []
             db.collection(K.FStore.collectionName).getDocuments { querySnapshot, error in
                 if let e = error{
                     print("Issue retriving data from firestore, \(e)")
                 }else{
                     if let snapshotDocuments = querySnapshot?.documents{
                         for doc in snapshotDocuments{
                             let data = doc.data()
                             if let sender = data[K.FStore.senderField] as? String, let body = data[K.FStore.bodyField] as? String{
                                 let newMessage = Message(sender: sender, body: body)
                                 self.messages.append(newMessage)
                                 DispatchQueue.main.async {
                                     self.tableView.reloadData()
                                 }
                             }
                         }
                     }
                 }
             }
         }
     ```

   - add and retrive data realtime: change getDocuments to addSnapshotListener

   - in closure, set the message = [] to avoid duplicating

   - sort data in database (base on created time), default ascending

     ```swift
     db.collection(K.FStore.collectionName).order(by: K.FStore.dateField).addSnapshotListener
     ```

   - change the database from public to secure: change in rules

     ```swift
     service cloud.firestore {
       match /databases/{database}/documents {
         match /{document=**} {
           allow read, write: if request.auth != null;
         }
       }
     }
     ```

10. Refine UI
    - move input textfield above keyboard: IQKeyboardManager https://github.com/hackiftekhar/IQKeyboardManager
    - use swift package: project - add package dependency - search github
    - in AppDelegate, import and set QKeyboardManager.shared.enable = **true**
    - Make navigation bar work well: set Appearance -> Standard and Appearance -> Scroll Edge in the Attributes panel for the Navigation bar of the Navigation Controller
    - set cell bubble background color to customed color: (named: xxx)

### Section 15

Shortcuts: https://github.com/0nn0/terminal-mac-cheatsheet

```
ls: list all files
cd: change directory, prefix+tab
cd~, cd..: back to upper level
control+A: move cursor to begining
control+E: move cursor to end
control+U: clear line
Ctrl + L: clear screen
Ctrl + Y: paste
mkdir: new folder
touch (name): new file
open (name): open file
open -a (application name) (name): open file with
rm (name): delete file
rm *: delete all files

```

### Section 16

1. change device:

   ContentView().previewDevice(PreviewDevice(rawValue: "iPhone13"))

2. Change text attributes: drag to text

3. Set background and ignore safe area: 

   ```swift
   ZStack{
               Color(.systemBlue)
                   .edgesIgnoringSafeArea()
               Text("I am Rich")
                   .font(.system(size: 40))
                   .fontWeight(.bold)
                   .foregroundColor(Color.white)
           }
   ```

4. embed in Stack: cmd+click

5. add custom fonts:
   - google fonts download
   - put the ttf file in 'fonts' group
   - Add 'Fonts provided by the application' - item 0 - value to its [name.ttf]

6. Image edit:

7. reuse code: cmd+click reusebale part, extract subview

8. add space: Spacer

9. change value inside a struct: use [@State] before a var

10. Tableview: List{}

11. **struct** Post: Identifiable: let list can auto order the elements by id

    ```swift
    struct Post: Identifiable{
        let id: String
        let title: String
    }
    
    // if we already have a identifier id:
    struct Post: Decodable, Identifiable{
        var id: String{
            return objectID
        }
        let objectID: String
    }
    ```

12. create a list storing those identificable items:

    ```swift
    List(posts,rowContent: { post in
                    Text(post.title)
        })
    ```

13. make data display on view: 

    ```swift
    class NetworkManager: ObservableObject{
      @Published var post = [Post]()
    }
    
    struct ContentView: View {
        @ObservedObject var networkManager = NetworkManager()
    }
    ```

14. connect two pages' transfer:

    ```swift
    NavigationLink(destination: DetailView(url: post.url)) {
                        HStack{
                            Text(String(post.points))
                            Text(post.title)
                        }
                    }
    ```

15. Display webview

    ```swift
    import Foundation
    import WebKit
    import SwiftUI
    
    struct WebView: UIViewRepresentable{
        let urlString: String?
        func makeUIView(context: Context) -> WKWebView {
            return WKWebView()
        }
        
        func updateUIView(_ uiView: WKWebView, context:Context) {
            if let safeString = urlString{
                if let url = URL(string: safeString){
                    let request = URLRequest(url: url)
                    uiView.load(request)
                }
            }
        }
    }
    ```

### Section 19

1. table view add checkbox:

   tableView.cellForRow(at: indexPath)?.accessoryType = .checkmark

2. add pop-up: UIAlert

   ```swift
   @IBAction func addButtonPressed(_ sender: UIBarButtonItem) {
           let alert  = UIAlertController(title: "Add New Todory Item", message: "", preferredStyle: .alert)
           
           let action = UIAlertAction(title: "Add Item", style: .default) { action in
               //what will happen once user pressed this button
           }
           
           alert.addAction(action)
           present(alert, animated: true, completion: nil)
       }
   ```

3. save data in user defaults

   user default is stored in a plist (with key and value), only suitable for small data, cannot hold custom type (class, struct)

   - Create [**let** defaults = UserDefaults.standard] in controller class

   - Add [**self**.defaults.set(**self**.itemArray, forKey: "TodoListArray")] in action

   - retrieve data from the plist:

     ```swift
     if let items = defaults.array(forKey: "TodoListArray") as? [String]{
                 itemArray = items
             }
     ```

4. Singleton: only one copy shared accross the project

   ```swift
   class Car{
   		static let singletonCar = Car()
   }
   
   let myCar = Car.singletonCar
   ```

5. NSCoder

   encoder

   ```swift
   func saveItems(){
           let encoder = PropertyListEncoder()
           do{
               let data = try encoder.encode(itemArray)
               try data.write(to:dataFilePath!)
           }catch{
               print("Error occuring item array, \(error)")
           }
           tableView.reloadData()
       }
   ```

   decoder

   ```swift
   func loadItems(){
           if let data = try? Data(contentsOf: dataFilePath!){
               let decoder = PropertyListDecoder()
               do{
                   itemArray = try decoder.decode([Item].self, from: data)
               }catch{
                   print("Error occuring item array, \(error)")
               }
               
           }
       }
   ```

6. Core Data

   - add core data section in AppDelegate

   - Add entry and attributes, make it [current product module]

   - in viewcontroller:

     ```swift
     let context = (UIApplication.shared.delegate as! AppDelegate).persistentContainer.viewContext
     
         func saveItems(){
             
             do{
                 try context.save()
             }catch{
                  print("Error saving context, \(error)")
             }
             tableView.reloadData()
         }
         
         func loadItems(){
             let request : NSFetchRequest<Item> = Item.fetchRequest()
             do{
                 itemArray = try context.fetch(request)
             }catch{
                 print("Error feching data, \(error)")
             }
             
         }
      //delete
     context.delete(itemArray[indexPath.row])
     itemArray.remove(at: indexPath.row)//does not change core data
     saveItems()
     
     ```

     

7. lazy variable: only get loaded up when a value is needed 

8. use search bar:

   - Add to main.storyboard

   - Viewcontroller inherit UISearchBarDelegate

   - Code, *** NSPredicate***

     ```swift
     func searchBarSearchButtonClicked(_ searchBar: UISearchBar) {
             let request: NSFetchRequest<Item> = Item.fetchRequest()
             request.predicate = NSPredicate(format: "title CONTAINS[cd] %@", searchBar.text!)
             request.sortDescriptors = [NSSortDescriptor(key: "title", ascending: true)]
             loadItems(with: request)
         }
     ```

   - Return to original List： add default parameter value


9. use graph editor to build data model relationship

10. didset: code in the closure will be triggered once the value is set

    ```swift
    var selectedCategory: Category?{
            didSet{
                //code here
            }
        }
    ```

11. Compound predicate

    ```swift
    func loadItems(with request: NSFetchRequest<Item> = Item.fetchRequest(), predicate: NSPredicate? = nil){
            
            let categoryPredicate = NSPredicate(format: "parentCategory.name MATCHES %@", selectedCategory!.name!)
            if let additionalPredicate = predicate{
                request.predicate = NSCompoundPredicate(andPredicateWithSubpredicates: [categoryPredicate, additionalPredicate])
            }else{
                request.predicate = categoryPredicate
            }
    }
    ```

12. Realm

    - initiate in appdelegate

    - create class data inherited from Object

    - with dynamic: allow variables be monitered from changes at runtime

      ```swift
      class Data: Object{
          @objc dynamic var name: String = ""
          @objc dynamic var age: Int = 0
      }
      ```

    - One-to-many relationship:

      ```swift
      let items = List<Item>()
      var parentCategory = LinkingObjects(fromType: Category.self, property: "items")
      ```

    - Write: realm.add(category)

      ```swift
              do{
                  try realm.write({
                      realm.add(category)
                  })
              }catch{
                  print("Error saving category, \(error)")
              }
      ```

    - change [Category] to Result<Category>!, and it is auto-update

    - load item:

      ```swift
      func loadItems(){
              todoItems = selectedCategory?.items.sorted(byKeyPath: "title", ascending: true)
              tableView.reloadData()
          }
      ```

    - delete item:

      ```swift
      try realm.write({
                          item.done = !item.done
                          realm.delete(item)
      })
      ```

    - 

13. ?? Nil coaelcing operator: xx ?? 1: if xx is nil, return 1

14. Add date: Date(), if app got crushed, delete the app and restart

15. SwipeCellKit

    ```swift
        //tableview datasource methods
        override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
                let cell = tableView.dequeueReusableCell(withIdentifier: "Cell", for: indexPath) as! SwipeTableViewCell
                cell.delegate = self
                return cell
        }
        
        func tableView(_ tableView: UITableView, editActionsForRowAt indexPath: IndexPath, for orientation: SwipeActionsOrientation) -> [SwipeAction]? {
            guard orientation == .right else {return nil}
            let deleteAction = SwipeAction(style: .destructive, title: "Delete") { action, indexPath in
                // handle action by updating model with deletion
                print("Item deleted")
    //            if let categoryForDeletion = self.categories?[indexPath.row]{
    //                do{
    //                    try self.realm.write({
    //                        self.realm.delete(categoryForDeletion)
    //                    })
    //                }catch{
    //                    print("Error deleting category, \(error)")
    //                }
    //            }
            }
            
            //customize the action appearence
            deleteAction.image = UIImage(named: "delete-icon")
            return [deleteAction]
        }
        
    func tableView(_ tableView: UITableView, editActionsOptionsForRowAt indexPath: IndexPath, for orientation: SwipeActionsOrientation) -> SwipeOptions {
            var options = SwipeTableOptions()
            options.expansionStyle = .destructive
            options.transitionStyle = .border
            return options
        }
    ```

16. seperate SwipeCell to parent class

    created a superclass(swipe cell)

    ```swift
    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
            let cell = super.tableView(tableView, cellForRowAt: indexPath)
            cell.textLabel?.text = categories?[indexPath.row].name ?? "No Category Added Yet"
            return cell
        }
    
    //override superclass's method
    //if this function calls superclass's method, then it will execute superclass's method first
    override func updateModel(at indexPath: IndexPath) {
            if let categoryForDeletion = self.categories?[indexPath.row]{
                do{
                    try self.realm.write({
                        self.realm.delete(categoryForDeletion)
                    })
                }catch{
                    print("Error deleting category, \(error)")
                }
            }
        }
    ```

    

17. Chameleon

    Pod file: pod 'ChameleonFramework/Swift', :git => 'https://github.com/wowansm/Chameleon.git', :branch => 'swift5'

    gradient color: **if** **let** color = FlatSkyBlue().darken(byPercentage: CGFloat(indexPath.row)/CGFloat(todoItems!.count)){

    Contrast text: cell.textLabel?.textColor = ContrastColorOf(color, returnFlat: **true**)

### Section 20 App Purchase

1. press cell action: cell.accessoryType = .disclosureIndicator

2. Deselect cell animation: tableView.deselectRow(at: indexPath, animated: **true**)

3. Package: **import** StoreKit

   Transactions have many states: purchasing, purchased, failed, restored ...

   ```swift
   //class
   class QuoteTableViewController: UITableViewController, SKPaymentTransactionObserver
   
   //view did load
   SKPaymentQueue.default().add(self)
   
       //MARK: - in app purchase methods
       func buyPremiumQuotes(){
           if SKPaymentQueue.canMakePayments(){
               let paymentRequest = SKMutablePayment()
               paymentRequest.productIdentifier = productId
               SKPaymentQueue.default().add(paymentRequest)
           }else{
               print("user cannot make payments")
           }
       }
       
       func paymentQueue(_ queue: SKPaymentQueue, updatedTransactions transactions: [SKPaymentTransaction]) {
           for transaction in transactions {
               if transaction.transactionState == .purchased{
                   //user payment successful
               }else if transaction.transactionState == .failed{
                   //user payment failed
               }
           }
       }
   
       @IBAction func restorePressed(_ sender: UIBarButtonItem) {
           SKPaymentQueue.default().restoreCompletedTransactions()
       }
   ```

   

### Section 21 Advanced Swift

1. stored vs computed proporities

   Must 'var' and specify data type

   if you are creating a function without input and output, consider computed proporties

   ```swift
   let pizzaInInches:Int = 10
   var numberOfSlices:Int{
       return pizzaInInches - 4
   }
   ```

2. get and set

   newValue is a predefined name!

   ```swift
   var numberOfSlices:Int{
       get {
           return pizzaInInches - 4
       }
       set{
           print("num is now a new value which is \(newValue)")
       }
   }
   ```

3. observed proporties

   ```swift
   var pizzaInInches:Int = 10{
       willSet{
           print(newValue)
       }
       didSet{
           print(oldValue)
       }
   }
   
   pizzaInInches = 6
   ```

4. access level

   Private, fileprivate, internal(default), public(do not allow classess and methods overridden), open

5. advanced optional

   ```swift
   guard let number = Double(displayLabel.text!) else{ //an optinal number
               fatalError("cannot convert display label to double")
           }
   ```

6. fold codes: cmd+option+left

7. structs live in the stack (more quickly accessed), value

   classes live in the heap, reference

8. tuples

   ```
   let t1 = ("Kate", 12)
   let t2 = (name: "Kate", age: 12)
   let t3: (name: String, age: Int)
   ```

9. guard let VS if let:

   guard let: really bad thing, few happen

   if let: expect happen

### Section 23

1. import coreML

   ```swift
   import CoreML
   import Vision
   
   class ViewController: UIViewController, UIImagePickerControllerDelegate, UINavigationControllerDelegate{}
   ```

2. add navigation controller: main.storyboard - yellow dot - editor - embed in - navigation bar

3. implement camera function

   ```swift
   let imagePicker = UIImagePickerController()
   override func viewDidLoad() {
           super.viewDidLoad()
           imagePicker.delegate = self
           //imagePicker.sourceType = .camera
     			imagePicker.sourceType = .photoLibrary 
           imagePicker.allowsEditing = true
   }
   
   @IBAction func cameraTapped(_ sender: UIBarButtonItem) {
           present(imagePicker, animated: true, completion: nil)
   }
   ```

   delegation method when user has picked the image

   ```swift
   func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]) {
           if let userPickedImage = info[UIImagePickerController.InfoKey.originalImage] as? UIImage{
               imageView.image = userPickedImage
       }
           imagePicker.dismiss(animated: true, completion: nil)
   }
   ```

4. using coreML model

   ```swift
       func detect(image: CIImage){
           guard let model = try? VNCoreMLModel(for: Inceptionv3().model) else{
               fatalError("loading coreML model failed")
           }
           let request = VNCoreMLRequest(model: model){(request, error) in
               guard let results = request.results as? [VNClassificationObservation] else{
                   fatalError("model failed to proceed image")
               }
               print(results)
           }
           let handler = VNImageRequestHandler(ciImage: image)
   //        try! handler.perform([request]) //force to execulate
           do {
               try handler.perform([request])
           }catch{
               print(error)
           }
       }
   ```

   

5. cocoa pods error: imcompatible xxx:

   sudo arch -x86_64 gem install ffi

   Then

   arch -x86_64 pod install

6. use Alamofire and SwiftyJSON to send http requests and parse json

   ```swift
   func requestInfo(flowerName: String){
           let params: [String: String] = [
               "format": "json",
               "action": "query",
               "prop": "extracts",
               "exintro": "",
               "titles": flowerName,
               "indexpageids": "",
               "redirects": "1",
           ]
           Alamofire.request(wikiUrl, method: .get, parameters: params).responseJSON { (response) in
               if response.result.isSuccess {
                   let flowerJson: JSON = JSON(response.result.value!)
                   let pageId = flowerJson["query"]["pageids"][0].stringValue
                   let extract = flowerJson["query"]["pages"][pageId]["extract"].stringValue
                   self.flowerDescription.text = extract
               }
           }
       }
   ```

7. SDWebImage

   ```swift
   // 1. add param
   "prop": "extracts|pageimages",
   "pithumbsize": "500"
   
   // 2. get flowerurl and display using sdwebimage
   let flowerUrl = flowerJson["query"]["pages"][pageId]["thumbnail"]["source"].stringValue
   self.imageView.sd_setImage(with: URL(string: flowerUrl))
   ```

8. makeAppIcon

### Section 25

1. use createML to train custom ML models

   ```swift
   import CreateMLUI
   let builder = MLImageClassifierBuilder()
   builder.showInLiveView()
   ```

   drag training data to the live view

   Save to mlmodel file

### Section 26

1. build mlmodel in macOS playground

   ```swift
   import Cocoa
   import CreateML
   
   let data = try MLDataTable(contentsOf: URL(fileURLWithPath: "/Users/katefu/XcodeProjects/Twittermenti-iOS13/train.csv"))
   let (trainingData, testingData) = data.randomSplit(by: 0.8, seed: 0)
   let sentimentClassifier = try MLTextClassifier(trainingData: trainingData, textColumn: "text", labelColumn: "label")
   let evaluationMetrics = sentimentClassifier.evaluation(on: testingData, textColumn: "text", labelColumn: "label")
   let evaluationAccuracy = (1.0 - evaluationMetrics.classificationError) * 100
   let metadata = MLModelMetadata(author: "Kate Fu", shortDescription: "A model trained to classifier tweets sentiments", version: "1.0")
   try sentimentClassifier.write(to: URL(fileURLWithPath: "/Users/katefu/XcodeProjects/Twittermenti-iOS13/TweetSentimentClassifier.mlmodel"))
   
   try sentimentClassifier.prediction(from: "@Apple is a terrible company")
   try sentimentClassifier.prediction(from: "I love you")
   ```

   

2. Swifter

   - git clone https://github.com/mattdonnelly/Swifter.git
   - drag the xcode project into your project just under the project name
   - in Twittermenti settings, add embed: SwifteriOS framework

## ![Screen Shot 2023-01-12 at 6.57.53 PM](/Users/katefu/Library/Application Support/typora-user-images/Screen Shot 2023-01-12 at 6.57.53 PM.png)

3. use swifter to get twitter data

   ```swift
   let swifter = Swifter(consumerKey: "", consumerSecret: "")
   ```


### Section 27

1. build AR project, iOS- AR app - SceneKit

2. build a cube

   ```swift
   let cube = SCNBox(width: 0.1, height: 0.1, length: 0.1, chamferRadius: 0.01)
           let material = SCNMaterial()
           material.diffuse.contents = UIColor.blue
           cube.materials =  [material]
           let node = SCNNode()
           node.position = SCNVector3(x: 0, y: 0.1, z: -0.5)
           node.geometry = cube
   ```

3. blender.org, turbosquid

4. measure detect surface automatically

   sceneView.debugOptions = [ARSCNDebugOptions.showFeaturePoints]

5. add a node

   ```swift
   func addDot(at hitResult: ARHitTestResult){
           let dotGeometry = SCNSphere(radius: 0.005)
           let material = SCNMaterial()
           material.diffuse.contents = UIColor.red
           dotGeometry.materials = [material]
           let dotNode = SCNNode(geometry: dotGeometry)
           dotNode.position = SCNVector3(x: hitResult.worldTransform.columns.3.x,
                                         y: hitResult.worldTransform.columns.3.y,
                                         z: hitResult.worldTransform.columns.3.z)
           sceneView.scene.rootNode.addChildNode(dotNode)
       }
   ```

6. add text geometry

   ```swift
   func updateText(text: String, atPosition position: SCNVector3){
           let textGeo = SCNText(string: text, extrusionDepth: 1.0)
           textGeo.firstMaterial?.diffuse.contents = UIColor.red
           let textNode = SCNNode(geometry: textGeo)
           textNode.position = SCNVector3(x: position.x, y: position.y+0.01, z: position.z)
           textNode.scale = SCNVector3(x: 0.01, y: 0.01, z: 0.01)
           sceneView.scene.rootNode.addChildNode(textNode)
       }
   ```


### Section 29

1. new Assets - new AR Resource Group - import Pokenmon Cards png, set width and height

2. add plane when image is detected:

   ```swift
   func renderer(_ renderer: SCNSceneRenderer, nodeFor anchor: ARAnchor) -> SCNNode? {
           let node = SCNNode()
           if let imageAnchor = anchor as? ARImageAnchor{
               let plane = SCNPlane(width: imageAnchor.referenceImage.physicalSize.width, height: imageAnchor.referenceImage.physicalSize.height)
               plane.firstMaterial?.diffuse.contents = UIColor(white: 1.0, alpha: 0.5) //transparent
               let planeNode = SCNNode(geometry: plane)
               planeNode.eulerAngles.x = -.pi/2 //逆时针90
               node.addChildNode(planeNode)
           }
           return node
       }
   ```

3. 

## Swift

official document: https://docs.swift.org/swift-book/GuidedTour/GuidedTour.html



### Grammer

#### Simple value

```swift
var myVariable = 42
let myConstant = 90

//If the initial value doesn’t provide enough information (or if there isn’t an initial value), specify the type by writing it after the variable, separated by a colon.
let explicitDouble: Double = 70
let explicitFloat: Float = 80

//If you need to convert a value to a different type, explicitly make an instance of the desired type.
let label = "The width is "
let width = 94
let widthLabel = label+String(width)

// include values in strings: Write the value in parentheses, and write a backslash (\) before the parentheses.
let apples = 3
let appleSummary = "I have \(apples) apples"

//Use three double quotation marks (""") for strings that take up multiple lines.
let quotation = """
I said I have \(apple) apples
what about you?
"""

var shoppingList = ["catfish","water","tulips"] //array
var occupation = ["Malcolm":"Captain", "Kaylee":"Mechanic"] //dict

// add element to array
shoppingList.append("blue paint")

// add or update element to dict
occupation["Kate"] = "Student"
occupation.updateValue("Doctor", forKey: "Malcolm")

//get an element from dict, with default value
occupation["Jam", default:"unknown"]

//create an empty array or dictionary, use the initializer syntax.
let emptyArray:[String] = []
let emptyArray = [Int]()
let emptyDict:[String: Float] = [:]
let emptyDict = [String:Float]()


//If type information can be inferred, you can write an empty array as [] and an empty dictionary as [:]
shoppingList = []
occupations = [:]

//set
let colors = Set(["red","green","blue"])
var emptySet = Set<String>()

//tuple, fixed size, can change value, cannot change type
var name = (first:"Tayler", last:"Swift")
name.0
name.first

//enum
enum Result{
  case success
  case failure
}
let result = Result.success
//enum associated values
enum Activity{
  case bored
  case running(destination:String)
  case talking (topic:String)
}
let talking = Activity.talking(topic:"sports")
//enum with raw value, default=0
enum Planet:Int{
  case mercury
  case venue
  case earth
}
let earth = Planet(rawValue:2)
//assign raw value when create, auto fill 1,2,3
enum Planet:Int{
  case mercury = 1
  case venue
  case earth
}

```

### Control Flow

```swift
let individualScores = [75,43,66,21,98]
var teamScore = 0
for score in individualScores{
  if score >50{
    teamScore+=3
  }else{
    teamScore+=1
  }
}


//An optional value either contains a value or contains nil to indicate that a value is missing.
var optionalString: String? = "Hello"

// null in swift is nil
print(optionalString == nil) //false

//Another way to handle optional values is to provide a default value using the ?? operator. If the optional value is missing, the default value is used instead.
let nickname: String? = nil
let fullName: String = "John Appleseed"
let informalGreeting = "Hi \(nickname ?? fullName)"

//switch
let vegetable = "red pepper"
switch vegetable {
case "celery":
    print("Add some raisins and make ants on a log.")
case "cucumber", "watercress":
    print("That would make a good tea sandwich.")
case let x where x.hasSuffix("pepper"):
    print("Is it a spicy \(x)?")
default:
    print("Everything tastes good in soup.")
}

// iterate dict
let interestingNumbers = [
    "Prime": [2, 3, 5, 7, 11, 13],
    "Fibonacci": [1, 1, 2, 3, 5, 8],
    "Square": [1, 4, 9, 16, 25],
]
var largest = 0
for (_, numbers) in interestingNumbers {
    for number in numbers {
        if number > largest {
            largest = number
        }
    }
}

//while
var n = 2
while n < 100 {
    n *= 2
}

var m = 2
repeat {
    m *= 2
} while m < 100

//keep an index in a loop by using ..< to make a range of indexes.
var total = 0
for i in 0..<4{
  total+=i
}

//give a label to the loop
outerLoop: for i in 1...10{
  for j in 1...10{
    let product = i*j
    print("\(i) * \(j) is \(product)")
  }
}

//repeat while
repeat{
  print("hello")
}while true


```

#### function and closures

```swift
func greet(person: String, day:String) -> String{
	return "Hello \(person), today is \(day)"
}
greet(person:"Bob",day:"Monday")

//Write a custom argument label before the parameter name, or write _ to use no argument label
func greet(_ person: String, on day: String) -> String {
    return "Hello \(person), today is \(day)."
}
greet("John", on: "Wednesday")

//Use a tuple to make a compound value—for example, to return multiple values from a function. The elements of a tuple can be referred to either by name or by number.

func calculateStatistics(scores: [Int]) -> (min: Int, max: Int, sum: Int) {}
let statistics = calculateStatistics(scores: [5, 3, 100, 3, 9])
print(statistics.sum)// Prints "120"
print(statistics.2)// Prints "120"

//Functions can be nested. Nested functions have access to variables that were declared in the outer function. 
func returnFifteen() -> Int {
    var y = 10
    func add() {
        y += 5
    }
    add()
    return y
}
returnFifteen()

//Functions are a first-class type. This means that a function can return another function as its value.
func makeIncrementer() -> ((Int) -> Int) {
    func addOne(number: Int) -> Int {
        return 1 + number
    }
    return addOne
}

//A function can take another function as one of its arguments.
func hasAnyMatches(list: [Int], condition: (Int) -> Bool) -> Bool {
    for item in list {
        if condition(item) {
            return true
        }
    }
    return false
}
func lessThanTen(number: Int) -> Bool {
    return number < 10
}
var numbers = [20, 19, 7, 12]
hasAnyMatches(list: numbers, condition: lessThanTen)

```

### Class

```swift
class Poodie:Dog{
   var color:String
   init(color:String, name:String){
       self.color = color
       super.init(name:name, breed = "Poodie")
   }
}
```

### Optional

1. Force wrapping:let text:String = myOprional!
2. check for nil value: if myOprional == nil:
3. Optional binding
4. nil coalescing operator: let text:String = myOprional ?? "default value"
5. optional chaining (for struct): myOptional?.property

### Protocol (interface)

```swift
//define the protocol
protocol MyProtocol{
  //define requirements
  func fly()
}
//adopt the protocol
struct MyStruct: MyProtocol{}
class MyClass: Myprotocol{
  func fly(){
    
  }
}

struct FlyingMuseum{
  func flyingDemo(flyingObject:CanFly){
    flyingObject.fly()
  }
}

//multiple inheritence: class first
class MyClass:Superclass, FirstProtocol, SecondProtocol{}
```

### Closure

```swift
//func parameter can be another func
func calculator(n1:Int, n2:Int, operation: (Int, Int)->Int)->Int{
  return operation(n1, n2);
}
func add(n1:Int, n2:Int)->Int{
  return n1+n2;
}
calculator(n1:2, n2:3, operation:add)

//closure sane as func add
{(n1:Int, n2:Int)->Int in
    return n1+n2
}

// same as func calculator
calculator(n1:2, n2:3, {(n1:Int, n2:Int)->Int in
    return n1+n2
})

//can ignore type and [return]
calculator(n1:2, n2:3, {(n1, n2) in
    n1+n2
})

//anonymous parameter name
// $0:first param ...
calculator(n1:2, n2:3, operation:{$0*$1})

//if func's last param is a closure{}
//can use trailing closure
calculator(n1:2, n2:3){$0*$1}

//map
let array = [239,109,21,314]
func addOne(n1:Int)->Int{
  return n1+1
}
array.map(addOne)

//to closure
array.map{$0+1}
```

### Extensions

we can extend class/protocol

```swift
extension Double{
    func round(to places:Int){
        let precisionNumber = pow(10, place)
        let n = self
        n = n * precisionNumber
        n.round()
        n = n/precisionNumber
        return n
    }
}
```

### Type Casting

https://docs.swift.org/swift-book/LanguageGuide/TypeCasting.html

Keyword: [as!]: force downcast, [is]: check, [as?]: safe casting

[Any]: anything, [AnyObject]: class, [NSObject]: Foundation class

## Kate Chat Project

selectors are an Objective-C concept, the func inside selector should be tagged @objc 
