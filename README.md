import React, { useState, useRef } from "react";
import './ListSort.css';

function ListSort() {
  const [fruitItems, setFruitItems] = useState(["Apple", "Banana", "Orange","Pineapple","karepe","Mango","Papaya"]);
  const [newFruitItem, setNewFruitItem] = useState("");
  const [selectedItems, setSelectedItems] = useState([]);
  const [showMoreStates, setShowMoreStates] = useState({}); // Store the showMore state for each item

  // References for drag-and-drop
  const dragItem = useRef(null);
  const dragOverItem = useRef(null);

  // Handle drag sorting
  const handleSort = () => {
    let _fruitItems = [...fruitItems];
    const draggedItemContent = _fruitItems.splice(dragItem.current, 1)[0];
    _fruitItems.splice(dragOverItem.current, 0, draggedItemContent);
    setFruitItems(_fruitItems);
    dragItem.current = null;
    dragOverItem.current = null;
  };

  // Handle name change
  const handleNameChange = (e) => {
    setNewFruitItem(e.target.value);
  };

  // Handle new item addition
  const handleAddItem = () => {
    if (newFruitItem.trim() !== "") {
      setFruitItems([...fruitItems, newFruitItem]);
      setNewFruitItem("");
    }
  };

  // Handle item click
  const handleItemClick = (index) => {
    if (selectedItems.includes(index)) {
      // If the item is already selected, remove it from selection
      setSelectedItems((prev) => prev.filter((item) => item !== index));
    } else {
      // Add item to the selection
      setSelectedItems((prev) => [...prev, index]);
    }
  };

  // Join selected items
  const handleJoin = () => {
    if (selectedItems.length > 1) {
      let _fruitItems = [...fruitItems];
      let combinedItem = selectedItems.map(index => _fruitItems[index]).join(' & ');

      // Remove the items from original list and replace with combined item
      _fruitItems = _fruitItems.filter((item, index) => !selectedItems.includes(index));
      _fruitItems.push(combinedItem);

      setFruitItems(_fruitItems);
      setSelectedItems([]);
    }
  };

  // Toggle "Show More" and "Show Less"
  const handleShowMoreToggle = (index) => {
    setShowMoreStates(prevStates => ({
      ...prevStates,
      [index]: !prevStates[index] // Toggle the showMore state for the specific item
    }));
  };

  return (
    <div className="out">
      <h2>Fruit List</h2>

      <div className="input-group">
        <input
          type="text"
          name="fruitName"
          placeholder="e.g Banana"
          value={newFruitItem}
          onChange={handleNameChange}
        />
        <button className="btn" onClick={handleAddItem}>
          Add item
        </button>
      </div>

      <div className="list-sort">
        {fruitItems.map((item, index) => (
          <div
            key={index}
            className={`list-item ${selectedItems.includes(index) ? "selected" : ""}`}
            draggable
            onDragStart={() => (dragItem.current = index)}
            onDragEnter={() => (dragOverItem.current = index)}
            onDragEnd={handleSort}
            onDragOver={(e) => e.preventDefault()}
            onClick={() => handleItemClick(index)}
          >
            {/* <i className="fa-solid fa-bars"></i> */}

            <span className="show-more-h3">
              {showMoreStates[index] ? item : `${item.substring(0, 5)}...`}
            </span>
            <button onClick={() => handleShowMoreToggle(index)}>
              {showMoreStates[index] ? "Show Less" : "Show More"}
            </button>
          </div>
        ))}
      </div>

      <button className="btn join-btn" onClick={handleJoin} disabled={selectedItems.length < 2}  style={{ color: selectedItems.length < 2 ? 'Green'  : 'Red' }} >
      Click here to add selected items to join
      </button>
    </div>
  );
}

export default ListSort;


