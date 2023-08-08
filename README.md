# Symbol-prefixed Comments for JSON
An approach which enables *symbol-prefixed comments* to be included in `JSON` data and then removed before the `JSON` data is parsed or processed.

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
// ["##1 THIS IS COMMENT 1",{"##2 THIS IS COMMENT 2":[],"The":["quick","##3 This is Comment 3","brown","##4 This is Comment 4","fox"]},["jumps"],"over","##5 This is Comment 5","the",{"lazy":"dog"}]

console.log(standardJSON);
// [{"The":["quick","brown","fox"]},["jumps"],"over","the",{"lazy":"dog"}]

```
