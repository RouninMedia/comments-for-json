# Symbol-prefixed Comments for JSON
An approach which enables *symbol-prefixed comments* to be included in `JSON` data and then removed before the `JSON` data is parsed or processed.

_______

## Step 1: Adding Comments

In this approach, comments are prefixed by `comment`-markers, analagous to `//` in javascript, which indicate that the remainder of the entry is a comment.

The default `comment`-marker is `##` but may be customised to be *any* string of *any* length.

# Example of JSON with symbol-prefixed Comments:

```json
[
  "##1 THIS IS COMMENT 1",

  {
    "##2 THIS IS COMMENT 2": [],
    "The": [
      "quick",
      "##3 This is Comment 3",
      "brown",
      "##4 This is Comment 4",
      "fox"
    ]
  },

  ["jumps"],
  "over",
  "##5 This is Comment 5",
  "the",
  {
    "lazy": "dog",
    "##6 This is Comment 6": "This can be any value"
  }
]
```

There are ***four*** things to note from the example above:

 - in the context of *array-notation*, comments will be written as *strings*: `"## This is a comment"`
 - in the context of *object-notation*, comments will be written in *key-value* notation: `"## This is a comment": "This can be any value"`
 - in the context of *object-notation*, comments are always written into the *key* and not the *value* 
 - in the context of *object-notation*, the *value* which follows a *comment-key* can be *anything*

_______

## Step 2: Removing Comments

```js
const removeComments = (myData, commentSyntax = '##') => {

  // HANDLE ARRAYS
  if (Array.isArray(myData)) {
  
    for (let i = (myData.length - 1); (i + 1) > 0; i--) { 
    
      if (typeof myData[i] === 'object') {
      
        myData[i] = removeComments(myData[i]);
      }
 
      else if ((typeof myData[i] === 'string') && (myData[i].substr(0, 2) === commentSyntax)) {
  
        myData = myData.slice(0, i).concat(myData.slice(i + 1));
      }
    }
  }

  // HANDLE OBJECTS
  else if (typeof myData === 'object') {
  
    let myDataEntries = Object.entries(myData);
    
    for (let i = (myDataEntries.length - 1); (i + 1) > 0; i--) {
    
      let myDataKey = myDataEntries[i][0];
      let myDataValue = myDataEntries[i][1];
      
      if (myDataKey.substr(0, 2) === commentSyntax) {
  
        myDataEntries = myDataEntries.slice(0, i).concat(myDataEntries.slice(i + 1));
      }
    
      else if (Array.isArray(myDataValue)) {
      
        myDataEntries[i][1] = removeComments(myDataValue);
      }
    }
    
    myData = Object.fromEntries(myDataEntries);
  }

  return myData;
}

let commentedJSON = '["##1 THIS IS COMMENT 1",{"##2 THIS IS COMMENT 2":[],"The":["quick","##3 This is Comment 3","brown","##4 This is Comment 4","fox"]},["jumps"],"over","##5 This is Comment 5","the",{"lazy":"dog"}]';

let myData = JSON.parse(commentedJSON);
myData = removeComments(myData);
standardJSON = JSON.stringify(myData);

console.log(commentedJSON);
// ["##1 THIS IS COMMENT 1",{"##2 THIS IS COMMENT 2":[],"The":["quick","##3 This is Comment 3","brown","##4 This is Comment 4","fox"]},["jumps"],"over","##5 This is Comment 5","the",{"lazy":"dog", "##6 This is Comment 6": "This can be any value"}]

console.log(standardJSON);
// [{"The":["quick","brown","fox"]},["jumps"],"over","the",{"lazy":"dog"}]

```
