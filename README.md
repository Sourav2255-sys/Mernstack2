# Mernstack2
Backend
create student-backend
cd student-backend
npm init -y
npm install express mongoose body-parser cors nodemon
//Create express server
Create a file called app.js in the root directory.


const express = require('express');
const mongoose = require('mongoose');
const bodyParser = require('body-parser');
const cors = require('cors');

// Initialize Express app
const app = express();
app.use(bodyParser.json());
app.use(cors());

// MongoDB connection
mongoose.connect('mongodb://localhost:27017/studentDB', { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log('Connected to MongoDB'))
  .catch((err) => console.error('MongoDB connection error:', err));

// Student schema and model
const studentSchema = new mongoose.Schema({
  name: String,
  age: Number,
  gender: String,
  district: String,
});

const Student = mongoose.model('Student', studentSchema);

// API endpoint to handle student data submission
app.post('/students', async (req, res) => {
  try {
    const student = new Student(req.body);
    await student.save();
    res.status(201).json({ message: 'Student added successfully' });
  } catch (error) {
    res.status(400).json({ error: 'Error adding student' });
  }
});

// Start the server
const PORT = 5000;
app.listen(PORT, () => console.log(`Server running on http://localhost:${PORT}`));

Start MongoDB and Express Server
mongod
nodemon app.js
Frontend
npx create-react-app frontend
cd frontend
npm install axios bootstrap react-bootstrap
src/App.js


import React, { useState } from 'react';
import axios from 'axios';
import 'bootstrap/dist/css/bootstrap.min.css';
import { Form, Button, Alert } from 'react-bootstrap';

function App() {
  const [student, setStudent] = useState({
    name: '',
    age: '',
    gender: 'Male',
    district: 'Ernakulam',
  });

  const [message, setMessage] = useState('');
  const [error, setError] = useState(false);

  const districts = ['Ernakulam', 'Thrissur', 'Kozhikode', 'Alappuzha', 'Kannur'];

  const handleChange = (e) => {
    const { name, value } = e.target;
    setStudent({ ...student, [name]: value });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      const response = await axios.post('http://localhost:5000/students', student);
      setMessage(response.data.message);
      setError(false);
      setStudent({ name: '', age: '', gender: 'Male', district: 'Ernakulam' });
    } catch (err) {
      setMessage('Error adding student');
      setError(true);
    }
  };

  return (
    <div className="container mt-5">
      <h2 className="text-center mb-4">Add Student Details</h2>
      {message && (
        <Alert variant={error ? 'danger' : 'success'}>{message}</Alert>
      )}
      <Form onSubmit={handleSubmit}>
        <Form.Group className="mb-3">
          <Form.Label>Name</Form.Label>
          <Form.Control
            type="text"
            name="name"
            value={student.name}
            onChange={handleChange}
            required
          />
        </Form.Group>

        <Form.Group className="mb-3">
          <Form.Label>Age</Form.Label>
          <Form.Control
            type="number"
            name="age"
            value={student.age}
            onChange={handleChange}
            required
          />
        </Form.Group>

        <Form.Group className="mb-3">
          <Form.Label>Gender</Form.Label>
          <div>
            {['Male', 'Female', 'Other'].map((option) => (
              <Form.Check
                inline
                key={option}
                type="radio"
                label={option}
                name="gender"
                value={option}
                checked={student.gender === option}
                onChange={handleChange}
              />
            ))}
          </div>
        </Form.Group>

        <Form.Group className="mb-3">
          <Form.Label>District</Form.Label>
          <Form.Select
            name="district"
            value={student.district}
            onChange={handleChange}
            required
          >
            {districts.map((district) => (
              <option key={district} value={district}>
                {district}
              </option>
            ))}
          </Form.Select>
        </Form.Group>

        <Button variant="primary" type="submit">
          Submit
        </Button>
      </Form>
    </div>
  );
}

export default App;
npm start
http://localhost:3000
