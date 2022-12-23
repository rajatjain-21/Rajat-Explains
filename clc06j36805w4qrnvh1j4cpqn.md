# Page visibility API to the rescue

### 🙋🏻‍♂️ User stories

*   I want to pause videos, image carousels, or animations when the user leaves the page
    
*   I want to stop downloading a heavy module or media when the user is not on the page currently?
    
*   I want to run some operations or processes in background on my page when the user is away?
    
*   How do I stop displaying live data from an API temporarily while the user is away.
    

Multiple problems, ✨one solution✨ - [Page Visibility API](https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API)

This is a little-known web API that rates last fourth in awareness in the [State of JS Survey](https://2021.stateofjs.com/en-US/features).

### 🏅 Page Visibility API

It lets you know when a user has left the page. To be precise, the API triggers an event whenever **the page visibility status changes**, either when the user minimizes or maximizes the window or switches the tab.

In the past, one had to use gimmicks to know if a user had switched tabs or minimized the window. The most popular was using the `blur` and `focus` browser events. Using these events would result in something like the following:

```javascript
window.addEventListener("focus", function () {
    // User is back on the page
    // Do Something
});

window.addEventListener("blur", function () {
    // User left the page
    // Do Something
});
```

The problem with this approach is that when the user used to click the browser search bar or the console or an alert dialog or even the window border, `blur` event used to kick off. So, `blur` and `focus` only tell us if the page is active but not if the content of the page is hidden or visible.

#### 💪 How to use it

The Page Visibility API brings two properties and an event to access the page visibility status:

*   `document.hidden`  
    It is globally available and read-only. Try to avoid it since it is now deprecated, but when accessed, it returns `true` if the page is hidden and `false` if it is visible.
    
*   `document.visibilityState`  
    It is the updated version of `document.hidden`, but when accessed, it returns four possible values depending on the page visibility status
    
    *   `visible`  
        The page is visible, or to be exact, it isn’t minimized nor in another tab.
        
    *   `hidden`  
        The page isn’t visible; it is minimized or in another tab.
        
    *   `prerender`  
        This is the initial state of a visible page when it is prerendering. A page’s visibility status may start at `prerender` and then change to another state, but it can’t change from another state to `prerender`.
        
    *   `unloaded`  
        The page is being unloaded from memory.
        
*   `visibilitychange`  
    It’s an event provided by the `document` object that is triggered when the page `visibilityState` changes.
    

```javascript
document.addEventListener("visibilitychange", () => {
    if (document.visibilityState === "visible") {
        // page is visible
    } else {
        // page is hidden
    }
});
```

### ⚡️Application

We will use the [**Fetch API**](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) to make a call to the `quotable.io` endpoint [https://api.quotable.io/random](https://api.quotable.io/random) and then insert it into the `quote` div.

Let’s shortly explain what is happening right here. We first select the `quote` element from the DOM. We then declare the `getQuote` function, which is an `async` function that allows us to fetch the data from the API. We use `setInterval()` to call the function every 10 seconds.

```javascript
const quote = document.querySelector("#quote");

const getQuote = async () => {
    try {
        const response = await fetch("https://api.quotable.io/random");
        const {content, author, dateAdded} = await response.json();
        const parsedQuote = `<q>${content}</q> <br> <p>- ${author}      </p><br><p>Added on ${dateAdded}</p>`;
        quote.innerHTML = parsedQuote;
    } catch (error) {
        console.error(error);
    }
};

getQuote();

setInterval(getQuote, 10000);
```

If the user minimizes the window or switches the tab, the page would still fetch the quotes, creating an unnecessary network load. To solve this, we can check if the page is visible before fetching a quote.

```javascript
const getQuote = async () => {
    if (document.visibilityState === "visible") {
        try {
            const response = await fetch("https://api.quotable.io/random");
            const {content, author, dateAdded} = await response.json();
            const parsedQuote = `
            <q>${content}</q> <br> 
            <p>- ${author}</p><br> 
            <p>Added on ${dateAdded}</p>`;
            quote.innerHTML = parsedQuote;
        } catch (error) {
            console.error(error);
        }
    }
};

getQuote();

setInterval(getQuote, 10000);
```

Now, we will only fetch the quote if the page is visible to the user.

Do you have ideas or topic you want me to blog about? Hit me up on [Twitter](https://twitter.com/rajatexplains)

---

If you liked what you read 🧑‍🏫 and got to learn new things, do hit like 👍 and subscribe 🔖 to my newsletter to get instantly notified whenever I drop in new content.

And don't forget to follow 🚀 me on

Hashnode - @[Rajat Jain](@rajatexplains)

Twitter - [@rajatexplains](https://twitter.com/rajatexplains)

Instagram - [@javascript\_to\_the\_rescue](https://instagram.com/javascript_to_the_rescue)

LinkedIn - [Rajat Jain](https://www.linkedin.com/in/rajatjain-21/)