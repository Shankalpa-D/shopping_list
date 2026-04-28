## Previously on ITEC 315...
- We learned how to use use input
- Now we will look more at this by building forms
	- Validation
	- Errors
	- Resets

---
## Challenge 1
- Take the files I'm giving you and round out the app to look like this:
- Notes
	- Use the main with the basic app
		- You can adjust
	- Also a data folder with Categories and dummy items
- We are importing some models that dont exist-> create these
- Create the enum!
- Need to make a category and grocery items model to do this
- GOGOGO!
---
## Challenge 2
- Make the UI!
- What do we need -> Some widgets duh...
- Make a grocery list widget->stateless for now
	- Use a scaffold
	- Set the Title 
	- For the body, use a ListView like we have done before but have it build a [ListTile](https://api.flutter.dev/flutter/material/ListTile-class.html) widget
	- populate it with the items
	- Look at the leading and trailing properties!

---
## User Input
- Lets create a new widget for this
- Call it new Item ->Make it stateful
```dart
import 'package:flutter/material.dart';

class NewItem extends StatefulWidget{
  const NewItem({super.key});
  
  @override
  State<NewItem> createState() {
    // TODO: implement createState
    return _NewItemState();
  }
  
}
class _NewItemState extends State<NewItem>{
  Widget build(BuildContext context)
  {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Add a new item")
      ),
      body:Padding(
        padding:const EdgeInsets.all(12),
      ),
      child:Text('Form')
    );
  }
}

```

---
## Changing Screens
- Now we need a button back on the original page to change to this screen
- We can do this in the app bar with actions
```dart
//In grocery_list.dart
 appBar: AppBar(title: const Text('Your Groceries'),
      actions: [
        IconButton(
          onPressed: (){},
          icon: Icon(Icons.add_box))
      ],
      ),
```
- Now we need to make the function to change screens
- We are going to use a new tool Navigator
	- This lets us look at the context 
- Navigator in Flutter is a widget that manages a stack of Route objects and provides methods for managing that stack, allowing you to move between different screens (or "routes") in your app.
- Works like a stack data structure - screens are pushed onto the stack when you navigate to them and popped off when you go back. The screen at the top of the stack is what the user sees.
- We use MaterialPageRoute(or cupertino) to transition to another page
- 
---
## Using it
- We need a stateful widget to use this however, it needs a context
- context not directly available to a widget in a stateless. SORRY FOR TELLING YOU TO MAKE IT THIS STOP YELLING AT ME!
- So we need to convert our whole widget
- NEW TRICK-> Right click the stateless, refactor as stateful
```dart
 void _addItem(){
    Navigator.of(context).push(
      MaterialPageRoute(builder: (ctx)=> const NewItem())
    );
  }
//Make sure to add the function to the onpressed!
```

---
## Form Widget
- Before we didnt use a form to take input
	- you dont need to
- It has more features, however, and is more appropriate for complicated input
- Form requires a child widget, can use normal stuff like Col
- For inputs, we now use form specific Widgets like TextFormField
```dart
class _NewItemState extends State<NewItem> {
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Add a new item")),
      body: Padding(
        padding: const EdgeInsets.all(12),
        child: Form(child: Column(children: [
          TextFormField(
            maxLength: 50,
            decoration: InputDecoration(
              label: Text('Name'),

            ),
          ),
          //instead of TextField like before
        ])),
      ),
    );
  }
}

```
---
## New Params
- Validator
	- Add a function that takes an automatic string and returns a string
	- Tells how validation will work, and displays this error message
	- We will talk more about this later
```dart
TextFormField(
	maxLength: 50,
	decoration: InputDecoration(
	  label: Text('Name'),

	),
	validator: (value){
	  return 'Error!';
	},
),
```
---
## More changes
- Want to add quantity of items and a dropdown for category
```dart
child: Column(
children: [
  TextFormField(
	maxLength: 50,
	decoration: InputDecoration(label: Text('Name')),
	validator: (value) {
	  return 'Error!';
	},
  ),//instead of TextField like before
  Row(children: [
	TextFormField(),
	const SizedBox(width:8),
	DropdownButtonFormField(items: items, onChanged: onChanged)
  ],)
],
),
```
- Not we are now using Form versions of these widgets
- Otherwise works the same as before
---
## Customizing the TextFormField
- We can add a decoration with a label
- Can also add a new property, initial value
	- Note that this is a text form field, so it takes a string value
```dart
TextFormField(
  decoration: InputDecoration(
	label: Text("Quantity"),
  ),
  initialValue: '1',  
),
```
---
## Dropdown
- We want to output the categories here
	- Can use this with an inlist for loop
- Categories is a map so we need to convert it to use a for loop using .entries
	- entries is a property of map that returns an array of keys and values
- Want to output a Dropdown menu item for each one
- We want a row in each so we can show the Colored box and the title of the item
```dart
DropdownButtonFormField(
	items: [
	  for (final category in categories.entries)
		DropdownMenuItem(
		  child: Row(
			children: [
			  Container(
				width: 16,
				height: 16,
				color: category.value.color,
			  ),
			  const SizedBox(width: 6),
			  Text(category.value.title),
			],
		  ),
		),
	],
	onChanged: onChanged,
),
```
---
## OnChanged
- Also need to have the value submitted for when it changes
- For this we use the value property
```dart
DropdownMenuItem(
  value:category.value,
```
- We could pass the enum and look it up later, but we will just pass the whole value
- Function ->just make a blank one with a value parameter for now
	- we will be getting the value passed through flutter
- `onChanged: (value){}),`
---
## Cleanup
- Right now this wont display yet
	- TextFormField is unconstrained width
	- So is row
	- So infinite width
- As before solve with expanded
	- On TextField and DropDown
- Also add a crossAxisAlignment.end to make it line up nicely
```dart
 crossAxisAlignment: CrossAxisAlignment.end,
	children: [
	  Expanded(
		child: TextFormField(
		  decoration: InputDecoration(label: Text("Quantity")),
		  initialValue: '1',
		),
	  ),
	  const SizedBox(width: 8),
	  Expanded(
		child: DropdownButtonFormField(
		//... More Below Unchanged
```
- Now we need to add some buttons and then look at form specific functionality
---
## Buttons
- Add a new row, below the other row, still in the col
- This will be for a reset and add button
```dart
Row(children: [
	TextButton(onPressed: (){},child:const Text("Reset")),
	ElevatedButton(onPressed: (){}, child: Text("Add Item"),),
  ],)
```
- Can also push these now to the other side with MainAxisAlignment.end
- Put a spacer for good measure
```dart
const SizedBox(height:12),
  Row(
	mainAxisAlignment: MainAxisAlignment.end,
	children: [
	  TextButton(onPressed: () {}, child: const Text("Reset")),
	  ElevatedButton(onPressed: () {}, child: Text("Add Item")),
	],
  ),
```
- Next we need to do functionality
	- Reset should clear
	- Add should do what we did in expense tracker
		- validate and save, potentially throw an error
---
## Validation of user input
- We have the validator parameter, we can use this to get validation logic and return an error message (null if no error)
```dart
validator: (value) {
  if (value == null ||
	  value.isEmpty ||
	  value.trim().length <= 1 ||
	  value.trim().length > 50) {
	return 'Must be between 1 and 50 characters long';
  }
  return null;
},
```
- We use an if statement to check for some bad inputs
	- null, empty, too short, too long
	- return an error message if any happen
	- if we get passed this, we return null (no error)
---
## Continuing
- Right now we have the logic, but we have not triggered the function to run
	- We will do this soon
- We also want to have checks in our other input for quantity
	- Null and empty are good
	- Also want to try to parse to int and see if it works
```dart
child: TextFormField(
  decoration: InputDecoration(label: Text("Quantity")),
  initialValue: '1',
  validator: (value) {
	if (value == null ||
		value.isEmpty ||
		int.tryParse(value)==null ||
		int.tryParse(value)!<=0) {
	  return 'Must enter a quantity greater than 0!';
	}
	return null;
  },
),
```
- Lastly we are also checking if the value is less than or equal to 0. 
	- need a null force operator to ensure that it works. We are safe because we did a null check
- We could do a validator for the dropdown, but when we add an initial value we won't need one. 
---
## Executing Logic
- Need to tell flutter for the form to execute all validators for the forms
- Access given through a key
	- we used before to identify
	- Now we are using a different key
- We will use a global key
```dart
class _NewItemState extends State<NewItem> {
  final _formKey = GlobalKey<FormState>();
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Add a new item")),
      body: Padding(
        padding: const EdgeInsets.all(12),
        child: Form(
          key:_formKey,
          child: Column(
          //...
```
- Global keys give access to underling widget
- Makes sure that the widget keeps its state when a rebuild happens
- This will track the errors
- Usually use global keys with forms
---
## Triggering Validation
- Also need a function to run
```dart
class _NewItemState extends State<NewItem> {
  final _formKey = GlobalKey<FormState>();

  void _saveItem(){}
  //...
```
- And tell this to trigger when we press the button
```dart
Row(
	mainAxisAlignment: MainAxisAlignment.end,
	children: [
	  TextButton(onPressed: () {}, child: const Text("Reset")),
	  ElevatedButton(onPressed: _saveItem, child: Text("Add Item")),
	],
  ),
```
- Need to trigger validation in this function with the global key
- We will use the formKey state and trigger a specific helper method called validate
- Also returns a boolean if it passed or failed validation
```dart
void _saveItem(){
    _formKey.currentState!.validate();
  }
```
- Should have validation now! try it!
---
## Resetting
- Can also use formKey to reset the form
- currentState has a function for this also
```dart
 TextButton(onPressed: () {
                    _formKey.currentState!.reset();
                  }, child: const Text("Reset")),
```
---
## Saving Data
- Need form key with the save method 
- When calling the save method
	- This will trigger an onSave function of the form
- This received the value of the form field you attach it to at the point of time save is executed
```dart
void _saveItem(){
    _formKey.currentState!.validate();
    _formKey.currentState!.save();
  }
```
and
```dart
TextFormField(
	maxLength: 50,
	decoration: InputDecoration(label: Text('Name')),
	validator: (value) {
	  if (value == null ||
		  value.isEmpty ||
		  value.trim().length <= 1 ||
		  value.trim().length > 50) {
		return 'Must be between 1 and 50 characters long';
	  }
	  return null;
	},
	onSaved: (value){},
  ),
```
- We can combine this with a new variable to save the data to in the state
 - We also need to run the save only if validation succeeds. Heres the bunch of changes:
 ```dart
 class _NewItemState extends State<NewItem> {
  final _formKey = GlobalKey<FormState>();
  var _enteredName = '';
  void _saveItem(){
    if(_formKey.currentState!.validate()){
      _formKey.currentState!.save();
    }
  }
  //...
  onSaved: (value){
	  _enteredName = value!;
	},
  
 ```
 - We have to force value, but because of our validator, we are guaranteed to not be null
 - We also dont need to set state because we are not rebuilding the screen. 
	 - We can add a print to confirm its working
 ---

## Doing quantity
```dart
class _NewItemState extends State<NewItem> {
  final _formKey = GlobalKey<FormState>();
  var _enteredName = '';
  var _enteredQuantity = 1;
  void _saveItem() {
    if (_formKey.currentState!.validate()) {
      _formKey.currentState!.save();
      print("Name: ${_enteredName}");
      print("Quantity: ${_enteredQuantity}");
    }
  }
  //...
  onSaved: (value) {
	_enteredQuantity = int.parse(value!);
  },
```
- parse is safe because we already used tryparse before
---
## The Dropdown
- We can use a var for this 
- This one does need a set state
	- category is used to set the value visibly on the screen
```dart
//...
var _selectedCategory = categories[Categories.vegetables]!;
//...
initialValue:_selectedCategory,
//...
onChanged: (value) {
setState(() {
  _selectedCategory = value!;
});
                        
```
---
## Passing back data
- Now want to manage a variable for this
- in our state class in groceryList
	- `final List<GroceryItem> _groceryItems = [];`
- How do we get data between screens?
- We can use navigator pop
- This allows us to pass data
	- We will pass a grocery item with the filled data
```dart
//new_item.dart
void _saveItem() {
    if (_formKey.currentState!.validate()) {
      _formKey.currentState!.save();
      //print("Name: ${_enteredName}");
      //print("Quantity: ${_enteredQuantity}");
      //print("Category: ${_selectedCategory.title}");
      Navigator.of(context).pop(GroceryItem(id: DateTime.now().toString(), name: _enteredName, quantity: _enteredQuantity, category: _selectedCategory));
    }
  }
```
- We can combine this with our addItem function
```dart
//grocery_list.dart
void _addItem() async{
    final newItem = await Navigator.of(context).push<GroceryItem>(
      MaterialPageRoute(builder: (ctx)=> const NewItem())
    );
  }
```
- Push yields a future that holds the data that may be returned by the screen we pushed onto our stack
- we can add the type annotation about what type will eventually be yielded
---
## Using the data
- May be groceryItem or null because we could hit cancel or back
- We should check for this before doing anything else
```dart
void _addItem() async{
    final newItem = await Navigator.of(context).push<GroceryItem>(
      MaterialPageRoute(builder: (ctx)=> const NewItem())
    );
    if(newItem == null){
      return;
    }
    else{
      setState(() {
        _groceryItems.add(newItem);
      });
    }
  }
```
- Also update all the vars to use the new var
- Can also remove the dummy_items import and file (if wanted)
---
## PRactice 
- Try adding 2 features
- Fallback text for when list is empty
- Remove item functionality
---
## Connecting a Back End
- Previously all data was saved on device
- Grocery items only stored locally
- Device lost/replace-> data gone
- Also cannot view on other devices
- Also need to be able to share data between users
	- Think about uber-> need to share data between drivers and customers
- We need some server
	- can be 3rd party or custom
	- Store data in a database on that server
	- Users can then interact with that data
---
## Back End Cont
- Communication will happen via HTTP
- Protocol for communicating between clients and servers
- Client sends an http request
	- i.e. send a request to store when new item is created
- Backend does something with this request
	- For previous, maybe save the data in a table of items for the user
- Send back a response about the success or failure of this action
---
## HTTP Requests
- Contain:
	- URL Address
	- Method (type of request)
	- Headers(meta data)
	- Body(data)
---
## Request Methods
- Get -> Fetch Data
- Post -> Add new data
- Put ->overwrite data
- Patch ->update data
- Delete->delete data
- These are typical, but the server logic will decide what happens for each.
---
## Response
- When sent back after a request
- Contain:
	- headers
	- body
---
## Firebase
- Google Service
	- We can use anything, but this will be an easy choice
- Has combination of services and database features that minimize the code we have to write for the back end
- Need a google account for this
---
## Account
- [Firebase](https://firebase.google.com/)
- Log in with a google account, can use your personal or school
- Go to console
- Create Project
- Click 'Get Started by setting up a Firebase Project'
- Name it
- Uncheck enable gemini in firebase
- Uncheck enable analytics
- Hit create project
---
## Firebase Setup
- Offers many other services, we will just use it as a dummy back end for our project
	- Keeping it simple
- Click Build
	- then click realtime database
- This allows us to send HTTP requests to a specific url for firebase to do actions for us
- [Firebase Documentation](https://firebase.google.com/docs/reference/rest/database)
	- This is the documentation of the urls you can send to and how to use them
	- We will look at it in action, but here is the good place to look for more information
----
## Setup Cont
- Click Create Database
- Leave the region
- Start in test mode
	- locked mode for real apps
- click enable
- Backend is now setup and we can go back to our flutter app
---
## Sending HTTP Requests
- In NewItem.dart
- Right now, our data is saved by saveItem
- This is only storing locally in memory
	- Not even permanent
- We need to be able to send HTTP requests
	- We will use a package for this
	- [flutter http](https://pub.dev/packages/http/install)
	- Grab the command from there to install
- import the package in new_item, but with new syntax
```dart
//...
import 'package:http/http.dart' as http;
//...
```
- This bundles all logic for the package into an object called http
- We can use this to send the data after saving and validating now
---
## Sending Cont
- We can see many methods on the http object
- Remember there are different types, we will match them semantically for firebase. 
- we will use post
	- needs a url/uri
	- If we look at the rest documentation for post we can see how it should look
- Needs a map for headers and body(data)
	- we need to send the item in our body
- Copy the URL from your database
- we will use the URL.https function
	- this does not need the protocol at the beginning
```dart
final url = Uri.https('shopping-practice-3bcf8-default-rtdb.firebaseio.com', 'shopping-list.json');
```
- Also needs a path
	- we can pick this path ourselves
- We can pick any name, up to you
	- creates a folder or node int he database
	- we will see it when we add data
- firebase requires the json part
---
## Updating the post
- In our post, pass it the Url
- Also needs headers named parameter
	- This is information about the data we are sending over(meta data)
		- We are including the format which will be json
- Last we need the data in a body header
	- When passing this, we specified the body is in json, so we must provide it that way
	- Import the `import 'dart:convert';` package
- This will convert data to json, best to do from a map which is similar
```dart
http.post(url, headers:{
        'Content-Type':'application/json',
      },
      body:json.encode({
        'name':_enteredName,
        'quantity':_enteredQuantity,
        'category': _selectedCategory.title,
      }),);
```
- We need to send all the info
- for category we send the title only, not the whole object, we  can use the title to convert later
- We also don't need the ID, firebase will create a unique Id for us
- Lets also comment out the pop
	- This way we wont navigate back
---
## Getting Data Back
- Need to do this in grocery list
- However, we do need to wait for the send request to complete
	- That way we have the most up to date information
- HTTP requests take time-> sending this over the internet, server gets it, processes it, does it, creates a response->we need to wait for this response
- only try seeing it after waiting for the response
- POST will return this
- Can do this with async like before, as this returns a future just like others we have use
```dart
oid _saveItem() async{
    if (_formKey.currentState!.validate()) {
      
    _formKey.currentState!.save();
     final url = Uri.https('shopping-practice-3bcf8-default-rtdb.firebaseio.com', 'shopping-list.json');
      final response = await http.post(url, headers:{
        'Content-Type':'application/json',
      },
      body:json.encode({
        'name':_enteredName,
        'quantity':_enteredQuantity,
        'category': _selectedCategory.title,
      }),);
    }
  }
```
---
## Response 
- Contains properties like status code
	- can use this to check success
	- lets just assume it works now
- can also get the body of the response
- firebase will send back json data as the response, we can see this in the documentation
- returns the key name and the id given back
	- we will use this stuff later
- Right now, lets just print
```dart
      print(response.body);
      print(response.statusCode);
      if(!context.mounted)
      {
        return;
      }
      Navigator.of(context).pop();
```
- Additionally, we need to put an if check
- this is checking that we still have the same context
	- because we are using context in an async, we might not have the same context by the time it resolves
- The if check is terminating the function if the context we are in is no longer mounted and we lose it
- Basically just popping the screen if it is still visible
- If we use, it will add the item now and take us back when done, but we dont see anyhting
---
## Fetching Data
 - In Grocery List, in add item
	 - keep the future, but we dont get the new item anymore
 - Can remove the if else due to that
 - We will now fetch via a get request
 - import http package like before
 - use get to grab the response
 ```dart
 void _addItem() async{
    final newItem = await Navigator.of(context).push<GroceryItem>(
      MaterialPageRoute(builder: (ctx)=> const NewItem())
    );
    final url = Uri.https(
        'shopping-practice-3bcf8-default-rtdb.firebaseio.com',
        'shopping-list.json',
      );
    final response = http.get(url);
  }
 ```
 - If we do this with a print, we will see nothing happens!
	 - not set to do it when the page loads first time
 ----

## Handling first load
- Lets take this into its own function so we can call it in add but also on load
```dart
void _loadItems() async {
    final url = Uri.https(
      'shopping-practice-3bcf8-default-rtdb.firebaseio.com',
      'shopping-list.json',
    );
    final response = await http.get(url);
    print(response);
  }

  void _addItem() async {
    final newItem = await Navigator.of(
      context,
    ).push<GroceryItem>(MaterialPageRoute(builder: (ctx) => const NewItem()));
    _loadItems();
  }
```
- Also how do we load items for the first time?
	- init state!
```dart
  @override
  void initState(){
    super.initState();
    _loadItems();
  }
```
- if we check we get the item of type response, can also look at the body with response.body
- take a look
	- we get back an id with a nested map/json object
- Now we need to use this to update ui
---
## Showing data
- can use json package to decode
- `final listData = json.decode(response.body);`
- don't forget to decode
- need to convert to grocery items from this
- This object will look a little complicated
```dart
final Map<String, Map<String, dynamic>> listData = json.decode(response.body);
```
- We have a map of strings to maps, and that sub map is strings to different types, hence dynamic
- Can now loop through it
```dart
 void _loadItems() async {
    final url = Uri.https(
      'shopping-practice-3bcf8-default-rtdb.firebaseio.com',
      'shopping-list.json',
    );
    final response = await http.get(url);
    // print(response.body);
    final Map<String, Map<String, dynamic>> listData = json.decode(
      response.body,
    );
    final List<GroceryItem> _loadedItems = [];
    for (final item in listData.entries) {
      final currentCat = categories.entries.firstWhere(
        (catItem) => catItem.value.title == item.value['category'],
      ).value;
      _loadedItems.add(
        GroceryItem(
          id: item.key,
          name: item.value['name'],
          quantity: item.value['quantity'],
          category: currentCat,
        ),
      );
    }
  }
```
---
## Showing on screen
- We need to override the list we have with groceryItems and populate with this list
- We can change groceryItems to not be dynamic
- Additionally we set the state to tell it to load up our item
```dart
List<GroceryItem> _groceryItems = [];
  void _loadItems() async {
    final url = Uri.https(
      'shopping-practice-3bcf8-default-rtdb.firebaseio.com',
      'shopping-list.json',
    );
    final response = await http.get(url);
    // print(response.body);
    final Map<String, dynamic> listData = json.decode(
      response.body,
    );
    final List<GroceryItem> _loadedItems = [];
    for (final item in listData.entries) {
      final currentCat = categories.entries.firstWhere(
        (catItem) => catItem.value.title == item.value['category'],
      ).value;
      _loadedItems.add(
        GroceryItem(
          id: item.key,
          name: item.value['name'],
          quantity: item.value['quantity'],
          category: currentCat,
        ),
      );
    }
    setState(() {
      _groceryItems = _loadedItems;
    });
  }
```
---
## Avoiding unnecessary requests
- We are already getting data in body when creating the item via the response
- we also are have all the data for the object on the creation page
	- just missing the id
- We can avoid the get request and just get the id and send that whole thing back 
```dart
//in new_item
final Map<String, dynamic> resData = json.decode(response.body);

      if(!context.mounted)
      {
        return;
      }
      Navigator.of(context).pop(GroceryItem(id:resData['name'], name: _enteredName, quantity: _enteredQuantity, category: _selectedCategory));
```
- On the other side of the page
```dart
//in grocery_list
void _addItem() async {
    final newItem = await Navigator.of(
      context,
    ).push<GroceryItem>(MaterialPageRoute(builder: (ctx) => const NewItem()));
    if(newItem ==null)
    {
      return;
    }
    setState(() {
      _groceryItems.add(newItem);
    });
  }
```
- Now we load initially, but we can take the data passed back most of the time
---
## Managing the Loading State
- When reloading we will actually see the 'no items yet' until the request completes
	- not a great user experience
	- maybe a loading spinner will help
- I can use a bool when page starts to is loading, and set to false after it loads
```dart
class _GroceryListState extends State<GroceryList> {
  var _isLoading = true;
  //...
  setState(() {
      _groceryItems = _loadedItems;
      _isLoading = false;
    });
  
```
- inside the build
```dart
if (_isLoading)
    {
      content = const Center(child: CircularProgressIndicator());
    }
```
---
## Add Item and reset
- I probably want to also disable add item and reset when loading as well
	- prevent the user from clicking the button too many times when a request to the db is going through
	- also show the spinner
- Handling the buttons
```dart

class _NewItemState extends State<NewItem> {
  final _formKey = GlobalKey<FormState>();
  var _enteredName = '';
  var _enteredQuantity = 1;
  var _selectedCategory = categories[Categories.vegetables]!;
  var _isSending = false;
  //...cont
void _saveItem() async {
if (_formKey.currentState!.validate()) {
  _formKey.currentState!.save();
  setState(() {
	_isSending = true;
  });
  //cont...
  Row(
	mainAxisAlignment: MainAxisAlignment.end,
	children: [
	  TextButton(
		onPressed:_isSending ? null: () {
		  _formKey.currentState!.reset();
		},
		child: const Text("Reset"),
	  ),
	  ElevatedButton(onPressed:_isSending ? null: _saveItem, child: Text("Add Item")),
	],
  ),
```
- Null sets a button to disabled, and we can use the ternary like before->functions are values!
---
## Button Text Temp
- Can also temp change the button text while loading
```dart
ElevatedButton(
	onPressed: _isSending ? null : _saveItem,
	child: _isSending
		? SizedBox(
			height: 16,
			width: 16,
			child: CircularProgressIndicator(),
		  )
		: Text("Add Item"),
  ),
```
- This is all super quick ,but the ternary does the job here too. 
- Sized box is just to give it a good size for this area
---
## Problem Scenarios
- Try using an invalid project id for our send request
- in loadItems Loading screen will load forever
- We also have an error in debug
	- we can have other errors, but this is a simulation for not getting data back
	- maybe firebase is offline, maybe no internet on device etc
- We can check on the response status to check for this
- for ours, 404 should pop
- All 400+ codes are bad (remember from http stuff in 305)
- We can check for specific errors and handle specificly, we will just do a more general approach
```dart
class _GroceryListState extends State<GroceryList> {
  var _isLoading = true;
  String? _error=""; //add this
  @override
  void initState() {
    super.initState();
    _loadItems();
  }

  List<GroceryItem> _groceryItems = [];
  void _loadItems() async {
    final url = Uri.https(
      'shopping-practice-3bcf8-default-rtdb.firebaseio.com',
      'shopping-list.json',
    );
    final response = await http.get(url);
    if(response.statusCode >400){
		setState(() {

_error = "Failed to fetch data. Try again later";

});
    }//check for errors and set message
    //cont...
     );
    }
    if (_error != null) {
      content = Center(child: Text(_error!));
    }
    return Scaffold(
      appBar: AppBar(
        title: const Text('Your Groceries'),
        actions: [IconButton(onPressed: _addItem, icon: Icon(Icons.add_box))],
      ),
      body: content,
    );
```
---
## Removing Items
- We can load and add items
- We can delete, but if we refresh they are still there because we are just removing locally
- RemoveItem function
	- need a delete request here
- This also needs to specify the item we need to delete
- This is part of the url, but structured similar to a folder
```dart
void _removeItem(GroceryItem item) {
    final url = Uri.https(
      'shopping-practice-3bcf8-default-rtdb.firebaseio.com',
      'shopping-list${item.id}.json',
    );
```
- Then send a delete request to this url
```dart
void _removeItem(GroceryItem item)  {
    final url = Uri.https(
      'shopping-practice-3bcf8-default-rtdb.firebaseio.com',
      'shopping-list${item.id}.json',
    );
    var response  =  http.delete(url);
    setState() {
      _groceryItems.remove(item);
    }
  }
```
- I can do async await here as well, but don't have to
	- just delete in the background
- because i'm deleting locally as well it should update the ui, and this will just continue in the background
---
## Errors
- To deal with errors we do need async await
- Get the respones like above, check above 400 and insert back into the list just like we did with the snack bar in the last app
```dart
void _removeItem(GroceryItem item) async {
    final index = _groceryItems.indexOf(item);
    setState(() {
      _groceryItems.remove(item);
    });
    final url = Uri.https(
      'shopping-practice-3bcf8-default-rtdb.firebaseio.com',
      'shopping-list${item.id}.json',
    );
    var response  =  await http.delete(url);
    if(response.statusCode >400)
    {
      setState(() {
        _groceryItems.insert(index, item);
      });
    }
  }
```