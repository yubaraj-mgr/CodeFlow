We have 10 features in this project, and we have complted 2 of them which is login and registration.

Bootstrap imporant keyboards for styling

- modals for edit
- offcanvas for drop menu side menu

1. REGISTRATION DATA FLOW

When user sends thier details from front end, Registration has to go through 3 steps and than only it will be ready and go the our main database.

    i. Make sure the data send my the user are valid just like you check on mongodb, but before going to mongo db, it has to go through the security check which in our case is a "Joi" package.

    ii. After it gets clearance from the Joi, it will not go startight towards the main database but we have to make  sure the origional password is bycrypt before it goes to our main database, for that we use "bcrypt" package to bcrypt our password.

    iii. After we hashPassword, we will add "emailValidationCode" (key:value )to the user details, which will be unique value for every user using "uuidv4()" which is a package.

    (After this three process, we will have the user information in our database with the emailValidation code and hashedPassword)

    iv. After the user information is stored in database with the unique emailValidation code, we will send them the email through "nodeMailer" which is a package saying verify the email and the link that consists of thier uniqueEmailValidation code and thier email


    v. After we send the verification email with the link that consist of thier email and validationCode, they have to click on the html click button to verify which will lead them to our front end which is based on the link that we send thorugh email on the click here link where we have set useState where we will grab the email and code using useSearchParams to get the params and than throw them to patch method to look for that item and update it, to run our patch method that makes the status active and validation code empty.


    vi. After it goes through the patch method, it will first find the user with the given email and email validation Code and update our status to "actice" and emailValidationCode to "".

2.  LOGIN DATA FLOW

    i. Everthing has to go through Joi validation first after that login data flow is very similar to the past project, only difference is we have to check if the status is "active" and the passowrd send my user is match to our hatched password using speacila hatch function given by bycrypt package

3.  Authentication-Login/Authorization

Four stages of auntenticaiton Authorization
Stage 1.

    I. When we login, before it says success, we will create 2 unique token where the payload will be just user email(sign and refresh).
    II. We will store sign access token in the its own table by creating schema and model, and for the refreh jwt, we will store that token in user table attached with user details.
    III. We will store same JSON token to session storage and local storage

    IV. Than Prem sir div some private routing thing which is 90% fornt end

Stage 2.

    i. Protect our Front end and backend
    ii.. Every time we call backend private server like categories, product and so on (only login and reset password is public, everythingelse is private),first we need to make a middleware that has a function to check whether the user is authorized or not, to know if user is authorized,  we need to send the token that we store earlier in the session storage or local strage as a Authorization header.
    iii. Server will verify the token whether it is valid or not, we we need to decode it with ( jwt.verify(authorization, process.env.password)) based on that it will respond data back to you else say no, you are not allowed

token expired means token is valid and it is matching with the database

    if(decoded === "jwt expired"){
        <!-- res.json is sending info to the front end -->
        return res.status(403).json({
            status: "error",
            message: "jwt expired"
        })
    }
        a. To know if the token is valid we need 2 things
            i. make sure after verificaiton of token we get the user info (we need email only, its the payload just like you see in jwt.io after pasting the token there)
            ii. Find the type:jwt and token:authorization from Session table to check if the given token matched with our session table database
            iii. Last step is to if existInDb findOneAdminUser({email:decoded.email}), if found than req.adminInfo = findOneAdminUser
            Iv. next()

<!-- When we just want to call the database and don't need a response than we don't need await -->

            iii. if jwt is expired in jwtHelper, delete jwt token from session table (findOneAndDelete(type:"jwt", token: token))

    iv. Thann throw above middleware in all the private router.
    if(all above is passed){
        next();
    }else{
        error.status = 401
        message = unauthorised
    }

Stage 3.
401 authenticate 403 unAuthorize

Currnelty our website only checks if the user is there in our global state, so whenever we refresh or go to new tab that user won't be there and we are logged out. To prevent that

1 Make sure if the access token is still there we should not be looged out.

<!-- Note if accessJwt expres (which is 15m) we will create new accessToken again and again till the refreshToke gets expired which is 30d -->

        a. Session storage is there even after refresh.
        b. SEssion storage is no there in the new tab

    For that we make {autoLogin Function }

    const autLogin = ()=>(dispatch)=>{
        1. const accessJwt = get the jwt from sessio storage
        2. const refreshJwt = get the jwt from local storage
        3. if(accessJwt){
            fetch the user and put the user details to the global user state in our store.
        }else if(refreshJwt){
            1. validate the refreshJwt and if valid is true
            create new accessJwt in the server,get the new accessjwt or send it to our front end and recall  get the user from newly fetched accessJwt
        }else{
            this means the user has logged out .
            remove both the token form session storage and do call admin logoutFunction() or just remoe it from the logout function itself and just call the function;
        }
    }

    I. When we login we will create unique token using JWt at the backend, Store it in the database and return it back to the client
    II. We will store same JSON token to session storage and local storage

Stage 4.

    I. When we login we will create unique token using JWt at the backend, Store it in the database and return it back to the client
    II. We will store same JSON token to session storage and local storage
