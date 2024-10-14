# coding-project-template

Run below code

    npm install

    npm run preview

**venueSlice.js:**

Let's step through the code in the venueSlide.js file. It contains code to slice the Redux state related to venue selection using createSlice from @reduxjs/toolkit.

The initial state consists of an array of venue objects, each representing a rentable room in the venue. A venue object has properties such as the thumbnail image, name, cost, and quantity.

The venueSlice.js file includes reducer functions incrementQuantity and decrementQuantity to manage the number of venue items in the state.

Note: On the last page of this lab, we provide you with the links for images from Pixabay, with appropriate citations, or you may find your own.

1. incrementQuantity():

-- This function handles incrementing the quantity of a venue item in the state. It receives an action containing the index of the item to be incremented.
-- It first checks if the item exists in the state at the provided index. If the item exists and it's an Auditorium Hall with a quantity greater than or equal to 3, it returns early without modifying the state.
-- Otherwise, it increments the quantity of the item by one.

2. decrementQuantity():

--This function handles decrementing the quantity of a venue item in the state. It receives an action containing the index of the item to be decremented.
-- It first checks if the item exists in the state at the provided index and if its quantity is greater than 0.
-- If both conditions are met, the quantity of the item will be decreased by one.

**Redux Store Setup**

Now we'll look at the store.js file.

-- Create the Redux store with the configureStore() function from @reduxjs/toolkit
-- The store.js file contains a reducer called venue(), imported from venueSlice.js
-- This code creates a global Redux store using the @reduxjs/toolkit\ configureStore() function so all components in the application can access the state managed by the venueReducer().

**ConferenceEvent Component**

Now let's walkthrough the relevant code from the ConferenceEvent.jsx file.

The useSelector() function retrieves venue items from the Redux store state.

const venueItems = useSelector((state) => state.venue);

It then defines event handlers like handleAddToCart(), and handleRemoveFromCart() to manage the increase and decrease quantities from the user interactions.

const handleAddToCart = (index) => {
    if (venueItems[index].name === "Auditorium Hall (Capacity:200)" && venueItems[index].quantity >= 3) {
        return; // Prevent further additions
    }
    dispatch(incrementQuantity(index));
};

const handleRemoveFromCart = (index) => {
    if (venueItems[index].quantity > 0) {
        dispatch(decrementQuantity(index));
    }
};

Next, it calculates the remaining number of available auditorium halls to three, so the user cannot request more than three.

const remainingAuditoriumQuantity = 3 - venueItems.find(item => item.name === "Auditorium Hall (Capacity:200)").quantity; 

When the user selects the buttons to increase or decrease the number of rooms, the system should calculate the cost for all selected rooms.

For this, we define a calculateTotalCost() function and declare a venueTotalCost.

const calculateTotalCost = (section) => {
    let totalCost = 0;
    if (section === "venue") {
        venueItems.forEach((item) => {
            totalCost += item.cost * item.quantity;
        });
    }
    return totalCost;
};
const venueTotalCost = calculateTotalCost("venue");

Function declaration:
-- The function is defined using arrow function syntax and assigned to the constant calculateTotalCost.
-- It takes one string parameter, section, that indicates the section is calculated.
Initialization of totalCost:
-- The totalCost variable is initialized to 0. This value will hold the cumulative total cost for the specified section.
Conditional check:
-- The function checks if the section passed as an argument equals the string "venue".
-- If true, the total cost for the venue items will be calculated.
Iteration over venueItems:
-- The venueItems items array represents an item with properties cost and quantity.
-- The forEach function iterates over each item in the venueItems array. For each item, it multiplies item.cost by item.quantity and adds the result to totalCost.
Return statement:
-- After the loop is complete, the function returns the calculated totalCost.
Function call:
-- The function calculateTotalCost is called with the "venue" argument, which calculates the total cost for the items in the "venue" section.
-- The result of this calculation is stored in the constant venueTotalCost.
-- Lastly, the component displays the total cost of all selected venue items.

<div className="total_cost">Total Cost: ${venueTotalCost}</div>


Is needed review why TOTAL COST FOR EVENT is being read it as NaN and Unit Costs doesnt have $


## Link to Practice Project instructions 

https://author-ide.skills.network/render?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJtZF9pbnN0cnVjdGlvbnNfdXJsIjoiaHR0cHM6Ly9jZi1jb3Vyc2VzLWRhdGEuczMudXMuY2xvdWQtb2JqZWN0LXN0b3JhZ2UuYXBwZG9tYWluLmNsb3VkL3dmTXF1bTFCNHVuaThvWmYyaUlUY0EvcHJhY3RpY2UlMjBwcm9qZWN0JTIwb3ZlcnZpZXctdjEubWQiLCJ0b29sX3R5cGUiOiJpbnN0cnVjdGlvbmFsLWxhYiIsImFkbWluIjpmYWxzZSwiaWF0IjoxNzE2MjEwMjIxfQ.0A3TAYPKdJS37TnCfswmWH84zuP877ZrW0FevsOCtVE 
