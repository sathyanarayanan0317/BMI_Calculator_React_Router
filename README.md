# Ex06 BMI Calculator
## Date: 29-10-2025

SATHYANARAYANAN M
212224040300

## AIM
To develop a responsive and interactive Body Mass Index (BMI) Calculator using React that allows users to input their height and weight, and calculates their BMI to categorize their health status (e.g., Underweight, Normal, Overweight, Obese).

## DESIGN STEPS

### STEP 1: Initialize React Project

<li>Create a new React app using create-react-app.</li>
<li>Install React Router using:</li>
npm install react-router-dom

### STEP 2: Set Up Routing

Create routing structure with react-router-dom:

<li>Home route (/) – Intro or Navigation</li>

<li>BMI Calculator route (/bmi)</li>

<li>Result route (/result)</li>

### STEP 3: Design the BMI Form Page

<li>Create a form to accept Height (in cm or m) and Weight (in kg).</li>

<li>On form submit, navigate to the result page with entered values via URL query params or context/state.</li>

## STEP 4: Handle Input Validation

<li>Check if height and weight are valid numbers.</li>

<li>Optionally, show error messages for invalid inputs.</li>

### STEP 5: Perform BMI Calculation

<li>In the result component:

<li>Extract height and weight from the route (URL or passed state).</li>

<li>Apply the BMI formula:</li>

![image](https://github.com/user-attachments/assets/ec785506-c96b-489e-8783-fb1a5d36101a)
​
 
<li>Convert height from cm to m if needed.</li></li>

### STEP 6: Display Result

<li>Show calculated BMI.</li>

<li>Show category based on BMI range:

<li>Underweight, Normal, Overweight, Obese, etc.</li></li>

### STEP 7: Navigation Options

<li>Provide a button to go back to the BMI form to calculate again.</li>

### STEP 8: Enhancements

<li>Add styling using CSS or Tailwind.</li>

## PROGRAM
BMICalculator.jsx
```
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';

export default function BMICalculator() {
  const [height, setHeight] = useState('');
  const [weight, setWeight] = useState('');
  const [unit, setUnit] = useState('cm');
  const [error, setError] = useState('');
  const navigate = useNavigate();

  const handleSubmit = (e) => {
    e.preventDefault();
    
    if (!height || !weight) {
      setError('Please enter both height and weight');
      return;
    }
    
    if (isNaN(height) || isNaN(weight)) {
      setError('Please enter valid numbers');
      return;
    }
    
    if (height <= 0 || weight <= 0) {
      setError('Values must be greater than zero');
      return;
    }
    
    setError('');
    navigate(`/result?height=${height}&weight=${weight}&unit=${unit}`);
  };

  return (
    <div className="neu-container">
      <h1 style={{ fontSize: '2rem', fontWeight: 'bold', marginBottom: '1.5rem' }}>BMI Calculator</h1>
      
      <form onSubmit={handleSubmit} style={{ display: 'flex', flexDirection: 'column', gap: '1.5rem' }}>
        <div>
          <label style={{ display: 'block', fontSize: '1.125rem', fontWeight: '500', marginBottom: '0.5rem' }}>
            Height ({unit})
          </label>
          <input
            type="number"
            value={height}
            onChange={(e) => setHeight(e.target.value)}
            className="neu-input"
            placeholder={`Enter height in ${unit}`}
          />
        </div>
        
        <div>
          <label style={{ display: 'block', fontSize: '1.125rem', fontWeight: '500', marginBottom: '0.5rem' }}>
            Weight (kg)
          </label>
          <input
            type="number"
            value={weight}
            onChange={(e) => setWeight(e.target.value)}
            className="neu-input"
            placeholder="Enter weight in kg"
          />
        </div>
        
        <div style={{ display: 'flex', alignItems: 'center', gap: '1rem' }}>
          <span style={{ fontSize: '1.125rem', fontWeight: '500' }}>Units:</span>
          <label style={{ display: 'flex', alignItems: 'center', gap: '0.5rem' }}>
            <input
              type="radio"
              style={{ width: '1.25rem', height: '1.25rem', accentColor: 'var(--accent)' }}
              checked={unit === 'cm'}
              onChange={() => setUnit('cm')}
            />
            <span>cm</span>
          </label>
          <label style={{ display: 'flex', alignItems: 'center', gap: '0.5rem' }}>
            <input
              type="radio"
              style={{ width: '1.25rem', height: '1.25rem', accentColor: 'var(--accent)' }}
              checked={unit === 'm'}
              onChange={() => setUnit('m')}
            />
            <span>m</span>
          </label>
        </div>
        
        {error && <p className="error-message">{error}</p>}
        
        <button
          type="submit"
          className="neu-btn"
          style={{ width: '100%', fontSize: '1.125rem', padding: '0.75rem' }}
        >
          CALCULATE BMI
        </button>
      </form>
    </div>
  );
}
```
BMIResult.jsx
```
import { useLocation, useNavigate } from 'react-router-dom';

export default function BMIResult() {
  const location = useLocation();
  const searchParams = new URLSearchParams(location.search);
  const navigate = useNavigate();
  
  const height = parseFloat(searchParams.get('height'));
  const weight = parseFloat(searchParams.get('weight'));
  const unit = searchParams.get('unit') || 'cm';
  
  // Convert height to meters if in cm
  const heightInMeters = unit === 'cm' ? height / 100 : height;
  const bmi = (weight / (heightInMeters * heightInMeters)).toFixed(1);
  
  let category = '';
  let categoryClass = '';
  
  if (bmi < 18.5) {
    category = 'Underweight';
    categoryClass = 'underweight';
  } else if (bmi >= 18.5 && bmi < 25) {
    category = 'Normal weight';
    categoryClass = 'normal';
  } else if (bmi >= 25 && bmi < 30) {
    category = 'Overweight';
    categoryClass = 'overweight';
  } else {
    category = 'Obese';
    categoryClass = 'obese';
  }
  
  return (
    <div className="neu-container">
      <h1 className="neu-title">Your BMI Result</h1>
      
      <div className="neu-content">
        <div className="neu-data">
          <p>Height: {height} {unit}</p>
          <p>Weight: {weight} kg</p>
        </div>
        
        <div className={`bmi-category ${categoryClass}`}>
          <p className="bmi-label">Your BMI is</p>
          <p className="bmi-value">{bmi}</p>
          <p className="bmi-category-text">{category}</p>
        </div>
        
        <div className="neu-buttons">
          <button
            onClick={() => navigate('/bmi')}
            className="neu-btn neu-btn-secondary"
          >
            Calculate Again
          </button>
          <button
            onClick={() => navigate('/')}
            className="neu-btn"
          >
            Back to Home
          </button>
        </div>
      </div>
    </div>
  );
}
```
APP.JS
```
import { useLocation, useNavigate } from 'react-router-dom';

export default function BMIResult() {
  const location = useLocation();
  const searchParams = new URLSearchParams(location.search);
  const navigate = useNavigate();
  
  const height = parseFloat(searchParams.get('height'));
  const weight = parseFloat(searchParams.get('weight'));
  const unit = searchParams.get('unit') || 'cm';
  
  // Convert height to meters if in cm
  const heightInMeters = unit === 'cm' ? height / 100 : height;
  const bmi = (weight / (heightInMeters * heightInMeters)).toFixed(1);
  
  let category = '';
  let categoryClass = '';
  
  if (bmi < 18.5) {
    category = 'Underweight';
    categoryClass = 'underweight';
  } else if (bmi >= 18.5 && bmi < 25) {
    category = 'Normal weight';
    categoryClass = 'normal';
  } else if (bmi >= 25 && bmi < 30) {
    category = 'Overweight';
    categoryClass = 'overweight';
  } else {
    category = 'Obese';
    categoryClass = 'obese';
  }
  
  return (
    <div className="neu-container">
      <h1 className="neu-title">Your BMI Result</h1>
      
      <div className="neu-content">
        <div className="neu-data">
          <p>Height: {height} {unit}</p>
          <p>Weight: {weight} kg</p>
        </div>
        
        <div className={`bmi-category ${categoryClass}`}>
          <p className="bmi-label">Your BMI is</p>
          <p className="bmi-value">{bmi}</p>
          <p className="bmi-category-text">{category}</p>
        </div>
        
        <div className="neu-buttons">
          <button
            onClick={() => navigate('/bmi')}
            className="neu-btn neu-btn-secondary"
          >
            Calculate Again
          </button>
          <button
            onClick={() => navigate('/')}
            className="neu-btn"
          >
            Back to Home
          </button>
        </div>
      </div>
    </div>
  );
}
```


## OUTPUT

![image](https://github.com/user-attachments/assets/74d47b4b-0957-4a27-b8b6-cc6c415fdf6a)
![image](https://github.com/user-attachments/assets/c3db1b83-cee1-4c85-899c-00c4d40838ff)
![image](https://github.com/user-attachments/assets/8bc6e0aa-0463-4d5b-af01-d8fc0a9af901)





## RESULT
The BMI Calculator successfully takes user input for height and weight, performs the BMI calculation in real-time using React state and event handling, and displays the BMI value along with the corresponding health category.
