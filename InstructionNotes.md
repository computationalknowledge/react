Let's use an alternative library for drag-and-drop functionality called @dnd-kit/core and @dnd-kit/sortable.

Firstly, install the necessary packages:

bash
Copy code
npm install @dnd-kit/core @dnd-kit/sortable
Now, I'll provide an updated App.js using the @dnd-kit library:

jsx
Copy code
import React, { useState } from 'react';
import { DndContext, closestCenter, PointerSensor, useSensor, useSensors } from '@dnd-kit/core';
import { arrayMove, SortableContext, verticalListSortingStrategy } from '@dnd-kit/sortable';
import { SortableItem } from './SortableItem';
import './App.css';

function App() {
    const [items, setItems] = useState([]);
    const [newItem, setNewItem] = useState('');

    const sensors = useSensors(useSensor(PointerSensor));
    
    const addItem = () => {
        if (newItem.trim() !== '' && !items.includes(newItem.trim())) {
            setItems(prevItems => [...prevItems, newItem.trim()]);
            setNewItem('');
        }
    };

    const deleteItem = (index) => {
        setItems(prevItems => prevItems.filter((item, i) => i !== index));
    };

    const handleDragEnd = (event) => {
        const {active, over} = event;

        if (active.id !== over.id) {
            setItems(prevItems => {
                const result = arrayMove(prevItems, active.index, over.index);
                return result;
            });
        }
    };

    return (
        <div className="App">
            <h1>Shopping List</h1>
            <div className="input-section">
                <input 
                    value={newItem} 
                    onChange={e => setNewItem(e.target.value)} 
                    placeholder="Add an item" 
                />
                <button onClick={addItem}>Add</button>
            </div>
            <DndContext 
                sensors={sensors} 
                collisionDetection={closestCenter}
                onDragEnd={handleDragEnd}
            >
                <SortableContext items={items} strategy={verticalListSortingStrategy}>
                    <ul className="items-list">
                        {items.map((item, index) => (
                            <SortableItem 
                                key={index} 
                                id={item} 
                                item={item} 
                                onDelete={() => deleteItem(index)}
                            />
                        ))}
                    </ul>
                </SortableContext>
            </DndContext>
        </div>
    );
}

export default App;
For the associated SortableItem.js:

jsx
Copy code
import React from 'react';
import { sortableHandle, useSortable } from '@dnd-kit/sortable';
import { CSS } from '@dnd-kit/utilities';

export function SortableItem({id, item, onDelete}) {
    const {
        attributes,
        listeners,
        setNodeRef,
        transform,
        transition,
    } = useSortable({id});

    const style = {
        transform: CSS.Translate.toString(transform),
        transition: transition,
    };

    return (
        <li ref={setNodeRef} style={style} {...attributes} {...listeners}>
            {item}
            <button onClick={onDelete} style={{marginLeft: "10px"}}>Delete</button>
        </li>
    );
}
Now, you should be able to drag items, prevent duplicate entries, and delete items from your list using the @dnd-kit library. If you still face any issues or need further customizations, please let me know.



