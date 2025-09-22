# Style Selected Menu Item

Indicating to users which menu item is selected is good practice. It helps users to better understand where they are in the system. This is especially useful when menus consist only of top-level items. 

https://github.com/stadium-software/menu-selected-item/assets/2085324/eedba632-1941-43e7-914d-7acd54fca4ae

## Version
1.1 Mobile menu styling bug fix

1.2 Integrated CSS with script

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
/*Stadium Script 1.2 https://github.com/stadium-software/menu-selected-item*/
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
loadCSS();
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
function loadCSS() {
    let moduleID = "stadium-selected-menu-item";
    if (!document.getElementById(moduleID)) {
        let cssMain = document.createElement("style");
        cssMain.id = moduleID;
        cssMain.type = "text/css";
        cssMain.textContent = `
.container:not(.mobile) .stadium-selected-item-menu {
    .active-menu-item,
    .active-menu-item:hover {
        background-color: var(--active-menu-item-background-color, var(--MENU-ITEM-FONT-COLOR));
        border-color: var(--active-menu-item-border-color, var(--MENU-ITEM-FONT-COLOR));
        cursor: default;
        > span {
            font-weight: var(--active-menu-item-font-weight, bold);
            color: var(--active-menu-item-font-color, var(--MENU-BACKGROUND-COLOR));
        }
    }
}
.container.mobile .stadium-selected-item-menu {
    .nav a.active-menu-item,
    .nav .open li:is(.dropdown-submenu) a.menu-link.active-menu-item,
    .navbar-default .navbar-nav a.active-menu-item:hover, 
    .nav > li > a.active-menu-item:hover,
    .nav a.active-menu-item > .caret:after {
        background-color: var(--active-menu-item-background-color, var(--MOBILE-MENU-SUB-ITEMS-FONT-COLOR));
        border-color: var(--active-menu-item-border-color, var(--MOBILE-MENU-SUB-ITEMS-FONT-COLOR));
        cursor: default;
        > span {
            font-weight: var(--active-menu-item-font-weight, bold);
            color: var(--active-menu-item-font-color, var(--MENU-BACKGROUND-COLOR));
        }
    }
}
        `;
        document.head.appendChild(cssMain);
    }
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
Variables exposed in the [*selected-menu-item-variables.css*](selected-menu-item-variables.css) file can be [customised](#customising-css).

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

## Upgrading Stadium Repos
Stadium Repos are not static. They change as additional features are added and bugs are fixed. Using the right method to work with Stadium Repos allows for upgrading them in a controlled manner. 

How to use and update application repos is described here: [Working with Stadium Repos](https://github.com/stadium-software/samples-upgrading)