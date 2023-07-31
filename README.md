# Version-2.-Katachii-Frontend
Description about Katachii Frontend Version 2.

-----

## Overview.

> A Community Platform where users discuss about recent news, stock & coin trading, and fun memes.
> 

### Mobile:

[<img src="https://img.youtube.com/vi/O_nE4jP34H0/hqdefault.jpg" width="600" height="300"
/>](https://www.youtube.com/embed/O_nE4jP34H0)

### PC:

[<img src="https://img.youtube.com/vi/hpHpoA0m6mQ/hqdefault.jpg" width="600" height="300"
/>](https://www.youtube.com/embed/hpHpoA0m6mQ)

## Key Features.

- **Register/find password with email verification (using node-mailer)**
    
[<img src="https://img.youtube.com/vi/h3jAhAf5PtU/hqdefault.jpg" width="600" height="300"
/>](https://www.youtube.com/embed/h3jAhAf5PtU)
    
- **Search posts by title, writer's nickname, and comment contents**
    
[<img src="https://img.youtube.com/vi/KzthyTvqZfE/hqdefault.jpg" width="600" height="300"
/>](https://www.youtube.com/embed/KzthyTvqZfE)
    
- **Writing, scraping, liking/disliking posts**
    
[<img src="https://img.youtube.com/vi/GLce1Pw1bO0/hqdefault.jpg" width="600" height="300"
/>](https://www.youtube.com/embed/GLce1Pw1bO0)
    
- **Leaving comments**
    
[<img src="https://img.youtube.com/vi/MVlECTF4VHM/hqdefault.jpg" width="600" height="300"
/>](https://www.youtube.com/embed/MVlECTF4VHM)
    
- **Sending direct messages between users**
    
[<img src="https://img.youtube.com/vi/ysvKq4QJhWc/hqdefault.jpg" width="600" height="300"
/>](https://www.youtube.com/embed/ysvKq4QJhWc)
    
- **Getting notifications when getting comments on the post**
    
[<img src="https://img.youtube.com/vi/IwiRxRBi_c4/hqdefault.jpg" width="600" height="300"
/>](https://www.youtube.com/embed/IwiRxRBi_c4)

-----

## [FE] Image Handler Function for React-Quill

Created an image handler function for the React-Quill editor to separately store images in Firebase, enabling storing and managing them in the DB as URLS instead of Base64.

```jsx
const imageHandler = () => {  
    const input = document.createElement('input');
    input.setAttribute('type', 'file');
    input.setAttribute('accept', 'image/*');
    input.click();
  
    input.addEventListener('change', async (e) => {
      try {
        // Save the image in firebase
        const image = e.target.files[0]
        const { url } = await imageUpload(image)
    
        // Insert image into the editor
        const editor = quillRef.current.getEditor()      
        const cursor = editor.getSelection(true) 
        editor.insertEmbed(cursor.index, 'image', url)

				// Move the cursor to the right side of the image for better user experience
        editor.setSelection(cursor.index + 1) 

      } catch (error) {
        const editor = quillRef.current.getEditor()
        const cursor = editor.getSelection(true)
        editor.deleteText(cursor.index, 1)
      }
      
    })
  }
```

## [BE] Middleware for auth and authorize

Created a middleware called ‘protect’ for protected routes so that only logged in users can access protected routes.

```jsx
exports.protect = asyncHandler(async (req, res, next) => {
  const authHeader = req.headers.authorization

  if(!authHeader) {
    return next(new ErrorResponse('You do not have access permission.', 401))
  }

  const accessToken = authHeader.split(' ')[1]
  const refreshToken = req.cookies.refreshToken

  if(!accessToken || !refreshToken) {
    return next(new ErrorResponse('You do not have access permission.', 401))
  }

  try {
    const decoded = await jwt.verify(accessToken, process.env.JWT_SECRET)
    const user = await DB.User.findOne({ where: { id: decoded.id } })
    req.user = user
    next()
  } catch (error) {
    return next(new ErrorResponse('You do not have access permission.', 401))
  }
});
```

```jsx
router
    .route('/')
		.post(protect, addPost)
```

## Tagging users in the comment and getting notification for it

Users can tag other users in comments and the tagged users will get notifications.

```jsx
// Front-End:

const getTaggedNickname = () => {
    const regex = /@[^\s]+/g
    const matchedWords = comment.match(regex)

    const formatted = matchedWords ? matchedWords.map(word => word.replace('@', '')) : []
    return formatted
}

const submitHandler = () => {
    const taggedNicknames = getTaggedNickname()
		...
}
```

```jsx
// Back-End:

const tags = comment.tags

if(tags.length) {
    const users = await DB.User.findAll({ 
        where: { 
           nickName: { [Sequelize.Op.in]: tags }
		    }
		}
)
            
const userAlarms = users.map(user => ({
    userId: user.dataValues.id,
    type: 'comment',
    targetId: req.body.postingId,
    targetPreview: formatStringLength(post.dataValues.title, ALARM_PREVIEW_TEXT_LENGTH),
     moveTo: `/post/${req.body.postingId}`
}))
        
const alarms = [...alarms, ...userAlarms] 

        
await DB.UserAlarm.bulkCreate(alarms, { transaction })
```
- **Point system where users can earn points through engagement & Giving points as a gift between users**
    
[<img src="https://img.youtube.com/vi/NtY__dOZ9A4/hqdefault.jpg" width="600" height="300"
/>](https://www.youtube.com/embed/NtY__dOZ9A4)
