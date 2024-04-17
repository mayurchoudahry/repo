const express = require('express')
const app = express();
const mongoose = require('mongoose')
require('dotenv').config()
const User =  require('./models/userModel')
app.use(express.json())

mongoose.connect(process.env.MONGODB_URL).then(()=>{
    console.log("mongoose is connected")
}).catch((err)=>{
    console.log(err)
})

app.post('/signup', async (req, res) => {
    try {
        const { name, email, password, passwordConfirm } = req.body;

        const existingUser = await User.findOne({ email }); // Use await here

        if (existingUser) {
            return res.status(400).json({
                message: 'Email already exists'
            });
        }

        const newUser = await User.create({ // Use await here
            name,
            email,
            password,
            passwordConfirm
        });

        res.status(200).json({
            message: 'SUCCESS',
            data: {
                newUser
            }
        });

    } catch (error) {
        res.status(500).json({
            error: error.message
        });
    }
});




app.listen(process.env.PORT,()=>{
    console.log('server is running')
})
