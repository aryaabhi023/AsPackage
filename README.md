
# Systematic Response Sender with Effective Error Handle

This package is designed to handle errors effectively and streamline your try-catch blocks in the backend when sending responses.

## Features

### Async handler
The `asynchandler` file contains your async code. You can throw errors inside it using `ApiError`, a class designed for this purpose.


### ApiResponse
`ApiResponse` provides a systematic response structure consisting of three elements: status code, data to be sent, and an optional message.

Here is the small example of this. 

#### Example
```
import {ApiError,ApiResponse,asyncHandler} from "are_package";
const register = asyncHandler(async (req, res) => {
  
  const { fullname, email, password, username } = req.body;
 
  if ([fullname, username, email, password].some((field) => !field || field.trim() === "")) {
    throw new ApiError(400, "Please fill all the fields");
}

  let user = await User.findOne({
    $or: [{ username }, { email }],
  });
  
  if (user) {
     throw new ApiError(409, "User already exists");
  }

  const newUser = new User({
    fullname,
    username,
    email,
    password,
  });

  if(!newUser)
    throw new ApiError(500, error.message || "Some error occurred while registering");
  await newUser.save();
  res.status(201).json(
    new ApiResponse(200,newUser,"User created successfully")
  ); 
});
```

### 

To handle errors in the frontend, you can use the following error handling middleware:
```
// Error handling middleware
app.use((err, req, res, next) => {
  if (err instanceof ApiError) {
      res.status(err.statusCode).json({
          success: false,
          message: err.message,
          errors: err.errors,
      });
  } else {
      res.status(500).json({
          success: false,
          message: "Internal Server Error",
      });
  }
});
```



