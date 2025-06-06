# Style Selected Menu Item

Indicating to users which menu item is selected is good practice. It helps users to better understand where they are in the system. This is especially useful when menus consist only of top-level items. 


https://github.com/stadium-software/menu-selected-item/assets/2085324/eedba632-1941-43e7-914d-7acd54fca4ae


## Version
1.1 Mobile menu styling bug fix

## Setup

### Application setup
Check the *Enable Style Sheet8 checkbox in the application properties

### Global Script Setup
1. Create a Global Script called "SelectedMenuItem"
2. Add the input parameters below to the Global Script
   1. MenuClass
3. Drag a JavaScript action into the script
4. Add the Javascript below unchanged into the JavaScript code property
```javascript
/*Stadium Script 1.0 https://github.com/stadium-software/menu-selected-item*/
let scope = this;
let menuClassInput = ~.Parameters.Input.MenuClass;
if (typeof menuClassInput == "undefined") {
    console.error("The MenuClass parameter is required");
    return false;
} 
let menu = document.querySelectorAll(".menu-container." + menuClassInput);
if (menu.length == 0) {
    console.error("The class '" + menuClassInput + "' is not assigned to any menu control");
    return false;
} else if (menu.length > 1) {
    console.error("The class '" + menuClassInput + "' is assigned to multiple menu controls. Controls using this script must have unique classnames");
    return false;
} else { 
    menu = menu[0];
}
menu.classList.add("stadium-selected-item-menu");
let arrPageName = window.location.pathname.split("/");
let pageName = arrPageName[arrPageName.length - 1];
let array = dmGet(menu, "Items");
let findItemNested = (arr, itemId, nestingKey) => (
  arr.reduce((a, item) => {
    if (a) return a;
    if (item.url === itemId) return item;
    if (item[nestingKey]) return findItemNested(item[nestingKey], itemId, nestingKey);
  }, null)
);
let ob = findItemNested(array, "/" + pageName, "items");
let menuEls = menu.querySelectorAll(".nav.navbar-nav a");
for (let i=0; i<menuEls.length; i++) {
    let itemText = Array.prototype.reduce.call(menuEls[i].querySelector(".dropdown-item-text").childNodes, function(a, b) { return a + (b.nodeType === 3 ? b.textContent : ''); }, '');
    if (itemText && ob && itemText.trim().toLowerCase() == ob.text.toLowerCase()) {
        menuEls[i].classList.add("active-menu-item");
    }
}
function dmGet(control, p){
    let getObjectName = (obj) => {
        let objname = obj.id.replace("-container","");
        do {
            let arrNameParts = objname.split(/_(.*)/s);
            objname = arrNameParts[1];
        } while ((objname.match(/_/g) || []).length > 0 && !scope[`${objname}Classes`]);
        return objname;
    };
    function getDMValues(ob, property) {
        let obname = getObjectName(ob);
        return scope[`${obname}${property}`];
    }
    return getDMValues(control, p);
}
```

### Page Setup
1. Drag a Menu control to the page
2. Add a unique classname of your choice to the menu control

### Page.Load Setup
1. Drag the Global Script called "SelectedMenuItem" into the Page.Load event handler
2. Provide values for the scripts input parameter
   1. MenuClass: The classname of the menu control (e.g. stadium-selected-men-item)

## CSS
The CSS below is required for the correct functioning of the module. Variables exposed in the [*selected-menu-item-variables.css*](selected-menu-item-variables.css) file can be [customised](#customising-css).

### Before v6.12
1. Create a folder called "CSS" inside of your Embedded Files in your application
2. Drag the two CSS files from this repo [*selected-menu-item-variables.css*](selected-menu-item-variables.css) and [*selected-menu-item.css*](selected-menu-item.css) into that folder
3. Paste the link tags below into the *head* property of your application
```html
<link rel="stylesheet" href="{EmbeddedFiles}/CSS/selected-menu-item.css">
<link rel="stylesheet" href="{EmbeddedFiles}/CSS/selected-menu-item-variables.css">
``` 

### v6.12+
1. Create a folder called "CSS" inside of your Embedded Files in your application
2. Drag the CSS files from this repo [*selected-menu-item.css*](selected-menu-item.css) into that folder
3. Paste the link tag below into the *head* property of your application
```html
<link rel="stylesheet" href="{EmbeddedFiles}/CSS/selected-menu-item.css">
``` 

### Customising CSS
1. Open the CSS file called [*selected-menu-item-variables.css*](selected-menu-item-variables.css) from this repo
2. Adjust the variables in the *:root* element as you see fit
3. Stadium 6.12+ users can comment out any variable they do **not** want to customise
4. Add the [*selected-menu-item-variables.css*](selected-menu-item-variables.css) to the "CSS" folder in the EmbeddedFiles (overwrite)
5. Paste the link tag below into the *head* property of your application (if you don't already have it there)
```html
<link rel="stylesheet" href="{EmbeddedFiles}/CSS/selected-menu-item-variables.css">
``` 
6. Add the file to the "CSS" inside of your Embedded Files in your application

**NOTE: Do not change any of the CSS in the 'selected-menu-item.css' file**

## Upgrading Stadium Repos
Stadium Repos are not static. They change as additional features are added and bugs are fixed. Using the right method to work with Stadium Repos allows for upgrading them in a controlled manner. 

How to use and update application repos is described here: [Working with Stadium Repos](https://github.com/stadium-software/samples-upgrading)