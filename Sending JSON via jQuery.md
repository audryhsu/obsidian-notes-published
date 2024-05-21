---
creation date: 2022-03-02 22:33
aliases: 
tags: 🖥️
---

###### [Sending JSON via jQuery](Sending%20JSON%20via%20jQuery.md)
```js

// serialize and submit form using jQuery
$(() => {

  $('form').on('submit', function(event) {
    event.preventDefault();
    let $emailField = $('input[name="email"]')
    let $nameField = $('input[name="name"]')

    let regex = /^[\w]+@[a-z]+[.][a-z]{2,}$/

    if ($emailField.val() ==='' || $nameField.val() === '') {
      alert('Inputs cannot be blank')
    } else if (!regex.test($emailField.val())) {
      alert('Invalid email')
    } else {

	let data = $(this).serialize()
	
       // let formData = new FormData($('form').get()[0])
	//let data = {}
   // let [email, name] = [...formData.values()]
   // data['email'] = email
  //  data['name'] = name

    $.ajax({
      url: $('form').attr('action'),
      type:'POST',
      data: data
    }).done((data, textStatus, jqXHR) => {

      if (jqXHR.status === 400) {
        alert('Couldnt add staff' + jqXHR.response)
      } else if (jqXHR.status === 201) {
        alert(`Staff added with id ${data.id}`)

      }
    }).fail((jqXHR, textStatus, errorThrown) => {
      alert('Failed: ' + textStatus);
      console.log('error thrown:', jqXHR.status +' '+ errorThrown);
    })
    }
  })

})

```

^1ad26c






---
Tags: 

Reference:

Related: 