# Nodemailer

Written: 3 May 2021

Gmail example.
```javascript
const nodemailer = require("nodemailer");

// Create reusable transporter object using the default SMTP transport
let transporter = nodemailer.createTransport({
  service: "gmail",
  auth: {
    user: "from@email.com",
    pass: "password"
  }
});

// Create mail options
const mailOptions = {
  from: "from@email.com",
  to: "to@email.com",
  subject: "Email subject line",
  text: "Email text"
};

// Send email
transporter.sendMail(mailOptions).then((response, error) => {
  if (error) {
    return console.log(error)
  }

  return console.log(response)
})
```

Resources:
- [Nodemailer website](https://nodemailer.com/)
- [Using Gmail](https://nodemailer.com/usage/using-gmail/)