**COMPLETE CODE** 

## avSlice.jsx

\`\`\`
import { createSlice } from "@reduxjs/toolkit";

export const avSlice = createSlice({
  name: "av",
  initialState: [
        {
        img: "",
        name: "Projectors",
        cost: 200,
        quantity: 0,
    },
    {
        img: "",
        name: "Speaker",
        cost: 35,
        quantity: 0,
    },
    {
        img: "",
        name: "Microphones",
        cost: 45,
        quantity: 0,
    },
    {
        img: "",
        name: "Whiteboards",
        cost: 80,
        quantity: 0,
    },

    {
        img: "",
        name: "Signage",
        cost: 80,
        quantity: 0,
    },

  ],


  reducers: {
    incrementAvQuantity: (state, action) => {
        const item = state[action.payload];
        if (item) {
            item.quantity++;
        }
    },
    decrementAvQuantity: (state, action) => {
        const item = state[action.payload];
        if (item && item.quantity > 0) {
            item.quantity--;
        }
    },
  },
});

export const { incrementAvQuantity, decrementAvQuantity } = avSlice.actions;

export default avSlice.reducer; 
\`\`\`

## archivo mealsSlice.jsx

\`\`\` 
import { createSlice } from '@reduxjs/toolkit';
export const mealsSlice = createSlice({
  name: 'meals',
  initialState: [
    { name: 'Breakfast', cost: 50, selected: false },
{ name: 'High Tea', cost: 25, selected: false },
{ name: 'Lunch', cost: 65, selected: false },
{ name: 'Dinner', cost: 70, selected: false },
   
  ],
  reducers: {
    toggleMealSelection: (state, action) => {
        state[action.payload].selected = !state[action.payload].selected;
  },
  },
});

export const { toggleMealSelection } = mealsSlice.actions;
export default mealsSlice.reducer;
\`\`\`

## Archivo store.js

\`\`\`
import { configureStore } from '@reduxjs/toolkit';
import venueReducer from './VenueSlice';
import avReducer from './avSlice';
import mealsReducer from './mealsSlice';

export default configureStore({
  reducer: {
    venue: venueReducer,
    av: avReducer,
    meals: mealsReducer,
  },
});
\`\`\`

## Archivo TotalCost.jsx

\`\`\`
import React, { useState, useEffect } from 'react';
import "./TotalCost.css";
const TotalCost = ({ totalCosts, ItemsDisplay }) => {
    const total_amount = totalCosts.venue + totalCosts.av + totalCosts.meals;
    return (
        <div className="pricing-app">
            <div className="display_box">
                <div className="header">
                    <p className="preheading"><h3>Total cost for the event</h3></p>
                </div>
                <div>
                <h2 id="pre_fee_cost_display" className="price">
    ${total_amount}
</h2>
<div className="render_items">
    <ItemsDisplay />
</div>
                </div>
            </div>
        </div>
    );
};
export default TotalCost;
\`\`\` 

## Archivo VenueSlice.jsx

\`\`\`
// venueSlice.js
import { createSlice } from "@reduxjs/toolkit";

export const venueSlice = createSlice({
  name: "venue",
  initialState: [
    {
      img: "",
      name: "Conference Room (Capacity:15)",
      cost: 3500,
      quantity: 0,
    },
    {
      img: "",
      name: "Auditorium Hall (Capacity:200)",
      cost: 5500,
      quantity: 0,
    },
    {
      img: "",
      name: "Presentation Room (Capacity:50)",
      cost: 700,
      quantity: 0,
    },
    {
      img: "",
      name: "Large Meeting Room (Capacity:10)",
      cost: 900,
      quantity: 0,
    },
    {
      img: "",
      name: "Small Meeting Room (Capacity:5)",
      cost: 1100,
      quantity: 0,
    },
  
  ],
  reducers: {
   
    incrementQuantity: (state, action) => {
      const { payload: index } = action;
      if (state[index]) {
        if (state[index].name === " Auditorium Hall (Capacity:200)" && state[index].quantity >= 3) {
          return;        }
        state[index].quantity++;
      }
    },
    decrementQuantity: (state, action) => {
      const { payload: index } = action;
      if (state[index] && state[index].quantity > 0) {
        state[index].quantity--;
      }
    },
  },
});

export const { incrementQuantity, decrementQuantity } = venueSlice.actions;
export default venueSlice.reducer;
\`\`\`

## Archivo ConferenceEvent.jsx

\`\`\`
import React, { useState } from "react";
import "./ConferenceEvent.css";
import TotalCost from "./TotalCost";
import { toggleMealSelection } from "./mealsSlice";
import { incrementAvQuantity, decrementAvQuantity } from "./avSlice";
import { useSelector, useDispatch } from "react-redux";
import { incrementQuantity, decrementQuantity } from "./venueSlice";
const ConferenceEvent = () => {
    const [showItems, setShowItems] = useState(false);
    const [numberOfPeople, setNumberOfPeople] = useState(1);
    const venueItems = useSelector((state) => state.venue);
    const avItems = useSelector((state) => state.av);
    const mealsItems = useSelector((state) => state.meals);
    const dispatch = useDispatch();
    const remainingAuditoriumQuantity = 3 - venueItems.find(item => item.name === "Auditorium Hall (Capacity:200)").quantity;

    
    const handleToggleItems = () => {
        console.log("handleToggleItems called");
        setShowItems(!showItems);
    };

    const handleAddToCart = (index) => {
        if (venueItems[index].name === "Auditorium Hall (Capacity:200)" && venueItems[index].quantity >= 3) {
          return; 
        }
        dispatch(incrementQuantity(index));
      };
    
      const handleRemoveFromCart = (index) => {
        if (venueItems[index].quantity > 0) {
          dispatch(decrementQuantity(index));
        }
      };
    const handleIncrementAvQuantity = (index) => {
    dispatch(incrementAvQuantity(index));
};

const handleDecrementAvQuantity = (index) => {
    dispatch(decrementAvQuantity(index));
};

const handleMealSelection = (index) => {
    const item = mealsItems[index];
    if (item.selected && item.type === "mealForPeople") {
        // Ensure numberOfPeople is set before toggling selection
        const newNumberOfPeople = item.selected ? numberOfPeople : 0;
        dispatch(toggleMealSelection(index, newNumberOfPeople));
    }
    else {
        dispatch(toggleMealSelection(index));
    }
};

const getItemsFromTotalCost = () => {
    const items = [];
    venueItems.forEach((item) => {
      if (item.quantity > 0) {
        items.push({ ...item, type: "venue" });
      }
    });
    avItems.forEach((item) => {
      if (
        item.quantity > 0 &&
        !items.some((i) => i.name === item.name && i.type === "av")
      ) {
        items.push({ ...item, type: "av" });
      }
    });
    mealsItems.forEach((item) => {
      if (item.selected) {
        const itemForDisplay = { ...item, type: "meals" };
        if (item.numberOfPeople) {
          itemForDisplay.numberOfPeople = numberOfPeople;
        }
        items.push(itemForDisplay);
      }
    });
    return items;
  };

    const items = getItemsFromTotalCost();

    const ItemsDisplay = ({ items }) => {
        console.log(items);
        return <>
            <div className="display_box1">
                {items.length === 0 && <p>No items selected</p>}
                <table className="table_item_data">
                    <thead>
                        <tr>
                            <th>Name</th>
                            <th>Unit Cost</th>
                            <th>Quantity</th>
                            <th>Subtotal</th>
                        </tr>
                    </thead>
                    <tbody>
                        {items.map((item, index) => (
                            <tr key={index}>
                                <td>{item.name}</td>
                                <td>${item.cost}</td>
                                <td>
                                    {item.type === "meals" || item.numberOfPeople
                                    ? ` For ${numberOfPeople} people`
                                    : item.quantity}
                                </td>
                                <td>{item.type === "meals" || item.numberOfPeople
                                    ? `${item.cost * numberOfPeople}`
                                    : `${item.cost * item.quantity}`}
                                </td>
                            </tr>
                        ))}
                    </tbody>
                </table>
            </div>
        </>
    };
    const calculateTotalCost = (section) => {
        let totalCost = 0;
        if (section === "venue") {
            venueItems.forEach((item) => {
                totalCost += item.cost * item.quantity;
            });
        } else if (section === "av") {
            avItems.forEach((item) => {
                totalCost += item.cost * item.quantity;
            });
        } else if (section === "meals") {
            mealsItems.forEach((item) => {
                if (item.selected) {
                  totalCost += item.cost * numberOfPeople;
                }
              });
        }
    return totalCost;
    };
    const venueTotalCost = calculateTotalCost("venue");
const avTotalCost = calculateTotalCost("av");
const mealsTotalCost = calculateTotalCost("meals");
    const navigateToProducts = (idType) => {
        if (idType == '#venue' || idType == '#addons' || idType == '#meals') {
          if (showItems) { // Check if showItems is false
            setShowItems(!showItems); // Toggle showItems to true only if it's currently false
          }
        }
      }
      const totalCosts = {
        venue: venueTotalCost,
        av: avTotalCost,
        meals: mealsTotalCost,
    };

    return (
        <>
            <navbar className="navbar_event_conference">
                <div className="company_logo">Conference Expense Planner</div>
                <div className="left_navbar">
                    <div className="nav_links">
                        <a href="#venue" onClick={() => navigateToProducts("#venue")} >Venue</a>
                        <a href="#addons" onClick={() => navigateToProducts('#addons')}>Add-ons</a>
                        <a href="#meals" onClick={() => navigateToProducts('#meals')}>Meals</a>
                    </div>
                    <button className="details_button" onClick={() => setShowItems(!showItems)}>
                        Show Details
                    </button>
                </div>
            </navbar>
            <div className="main_container">
                {!showItems
                    ?
                    (
                        <div className="items-information">
                             <div id="venue" className="venue_container container_main">
        <div className="text">
 
          <h1>Venue Room Selection</h1>
        </div>
        <div className="venue_selection">
          {venueItems.map((item, index) => (
            <div className="venue_main" key={index}>
              <div className="img">
                <img src={item.img} alt={item.name} />
              </div>
              <div className="text">{item.name}</div>
              <div>${item.cost}</div>
     <div className="button_container">
        {venueItems[index].name === "Auditorium Hall (Capacity:200)" ? (

          <>
          <button
            className={venueItems[index].quantity === 0 ? "btn-warning btn-disabled" : "btn-minus btn-warning"}
            onClick={() => handleRemoveFromCart(index)}
          >
            &#8211;
          </button>
          <span className="selected_count">
            {venueItems[index].quantity > 0 ? ` ${venueItems[index].quantity}` : "0"}
          </span>
          <button
            className={remainingAuditoriumQuantity === 0? "btn-success btn-disabled" : "btn-success btn-plus"}
            onClick={() => handleAddToCart(index)}
          >
            &#43;
          </button>
        </>
        ) : (
          <div className="button_container">
           <button
              className={venueItems[index].quantity ===0 ? " btn-warning btn-disabled" : "btn-warning btn-plus"}
              onClick={() => handleRemoveFromCart(index)}
            >
               &#8211;
            </button>
            <span className="selected_count">
              {venueItems[index].quantity > 0 ? ` ${venueItems[index].quantity}` : "0"}
            </span>
            <button
              className={venueItems[index].quantity === 10 ? " btn-success btn-disabled" : "btn-success btn-plus"}
              onClick={() => handleAddToCart(index)}
            >
             &#43;
            </button>
            
            
          </div>
        )}
      </div>
            </div>
          ))}
        </div>
        <div className="total_cost">Total Cost: ${venueTotalCost}</div>
      </div>

                            {/*Necessary Add-ons*/}
                            <div id="addons" className="venue_container container_main">


                                <div className="text">

                                    <h1> Add-ons Selection</h1>

                                </div>
                                <div className="addons_selection">
                                {avItems.map((item, index) => (
    <div className="av_data venue_main" key={index}>
        <div className="img">
            <img src={item.img} alt={item.name} />
        </div>
    <div className="text"> {item.name} </div>
    <div> ${item.cost} </div>
        <div className="addons_btn">
            <button className="btn-warning" onClick={() => handleDecrementAvQuantity(index)}> &ndash; </button>
            <span className="quantity-value">{item.quantity}</span>
            <button className=" btn-success" onClick={() => handleIncrementAvQuantity(index)}> &#43; </button>
        </div>
    </div>
))}
                                </div>
<div className="total_cost">Total Cost: {avTotalCost}</div>
                            </div>

                            {/* Meal Section */}

                            <div id="meals" className="venue_container container_main">

                                <div className="text">

                                    <h1>Meals Selection</h1>
                                </div>

                                <div className="input-container venue_selection">
                                <div className="input-container venue_selection">
    <label htmlFor="numberOfPeople"><h3>Number of People:</h3></label>
    <input type="number" className="input_box5" id="numberOfPeople" value={numberOfPeople}
        onChange={(e) => setNumberOfPeople(parseInt(e.target.value))}
        min="1"
    />
</div>
                                </div>
                                <div className="meal_selection">
    {mealsItems.map((item, index) => (
        <div className="meal_item" key={index} style={{ padding: 15 }}>
            <div className="inner">
                <input type="checkbox" id={ `meal_${index}` }
                    checked={ item.selected }
                    onChange={() => handleMealSelection(index)}
                 />
                <label htmlFor={`meal_${index}`}> {item.name} </label>
            </div>
            <div className="meal_cost">${item.cost}</div>
        </div>
    ))}
</div>
<div className="total_cost">Total Cost: {mealsTotalCost}</div>

                            </div>
                        </div>
                    ) : (
                        <div className="total_amount_detail">
    <TotalCost totalCosts={ totalCosts } ItemsDisplay={() => <ItemsDisplay items={ items } />} />
</div>
                    )
                }




            </div>
        </>

    );
};

export default ConferenceEvent;
\`\`\` 