---
creation date: 2022-03-20 16:24
aliases: 
tags: 🖥️
---

# [ReactJS Forms](ReactJS%20Forms.md)
---
A standard way to access form data when it has been submitted by a user is to use a technique called *controlled components*. In standard HTML, form and input elements maintain their own state and update based on user input. In React, [state](./React.md#Component%20State) is kept in the [state property of components](./React.md#Special%20state%20properties)  and only updated with `setState` as the single source of truth.

An input form element whose value is controlled by React in this way is called a “controlled component”.

How does this work?
- the input's `value` attribute is set to the component's state. Now React is the single source of truth.
- a `onChange` event handler synchronizes updates between the input element and the component state via `setState` function
- Instead of using `addEventListener` to the `form`, we add a `onSubmit` attribute on the form element and pass an event handler callback as the prop. 


# Examples
```js
const App = (props) => {
  const [notes, setNotes] = useState(props.notes)
  
// controlled component stores user-submitted input
  const [newNote, setNewNote] = useState('a new note..') 

  const addNote = (event) => {
    event.preventDefault()
    
    const noteObject = {
      content: newNote, 
      date: new Date().toISOString(),
      important: Math.random() < 0.5,
      id: notes.length + 1,
    }
    setNotes(notes.concat(noteObject)) // update state
    setNewNote('') // reset controlled component 
  }
	// synchronizes changes btwn input & component's state
  const handleNoteChange = (e) => setNewNote(e.target.value) 

  return (
    //...
      <form onSubmit={addNote}> // submit handler
        <input
          value={newNote} // controlled component 
          onChange={handleNoteChange} /> // sync state
      </form>
  )
}
```



---
Tags: [Programming](Programming.md) - [React](./React.md) 

Reference:

Related: 