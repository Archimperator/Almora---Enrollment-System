const form = document.getElementById('enrollForm');
const courseSelect = document.getElementById('course');
const majorBox = document.getElementById('majorBox');
const statusMsg = document.getElementById('statusMsg');

// Show Major dropdown only if BSIT is picked
courseSelect.addEventListener('change', function() {
  if (this.value === 'BSIT') {
    majorBox.style.display = 'block';
  } else {
    majorBox.style.display = 'none';
    document.getElementById('major').value = '';
    clearError('major');
  }
});

// Clear red warnings on keypress or change
const inputIds = [
  'studentId', 'prefix', 'firstName', 'middleName', 
  'lastName', 'suffix', 'email', 'course', 'major', 'yearLevel'
];

inputIds.forEach(id => {
  const el = document.getElementById(id);
  el.addEventListener('input', () => clearError(id));
  el.addEventListener('change', () => clearError(id));
});

// Submit Form Logic
form.addEventListener('submit', function(e) {
  e.preventDefault();
  
  // Reset message box
  statusMsg.className = 'status-msg';
  statusMsg.textContent = '';

  let isValid = true;

  // Student ID
  const studentId = document.getElementById('studentId').value.trim();
  if (!studentId) {
    setError('studentId', 'Student ID is required.');
    isValid = false;
  } else if (studentId.length < 5) {
    setError('studentId', 'Minimum 5 characters required.');
    isValid = false;
  }

  // Prefix (Optional, min 2)
  const prefix = document.getElementById('prefix').value.trim();
  if (prefix && prefix.length < 2) {
    setError('prefix', 'Min 2 characters.');
    isValid = false;
  }

  // First Name
  const firstName = document.getElementById('firstName').value.trim();
  if (!firstName) {
    setError('firstName', 'First name is required.');
    isValid = false;
  } else if (firstName.length < 3) {
    setError('firstName', 'Minimum 3 characters required.');
    isValid = false;
  }

  // Middle Name (Optional, min 2)
  const middleName = document.getElementById('middleName').value.trim();
  if (middleName && middleName.length < 2) {
    setError('middleName', 'Min 2 characters.');
    isValid = false;
  }

  // Last Name
  const lastName = document.getElementById('lastName').value.trim();
  if (!lastName) {
    setError('lastName', 'Last name is required.');
    isValid = false;
  } else if (lastName.length < 2) {
    setError('lastName', 'Minimum 2 characters required.');
    isValid = false;
  }

  // Suffix (Optional, min 2)
  const suffix = document.getElementById('suffix').value.trim();
  if (suffix && suffix.length < 2) {
    setError('suffix', 'Min 2 characters.');
    isValid = false;
  }

  // Email
  const email = document.getElementById('email').value.trim();
  const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  if (!email) {
    setError('email', 'Email is required.');
    isValid = false;
  } else if (!emailPattern.test(email)) {
    setError('email', 'Enter a valid email address.');
    isValid = false;
  }

  // Course
  const course = document.getElementById('course').value;
  if (!course) {
    setError('course', 'Please select a course.');
    isValid = false;
  }

  // Major (BSIT only)
  const major = document.getElementById('major').value;
  if (course === 'BSIT' && !major) {
    setError('major', 'Select a major for BSIT.');
    isValid = false;
  }

  // Year Level
  const yearLevel = document.getElementById('yearLevel').value;
  if (!yearLevel) {
    setError('yearLevel', 'Please select year level.');
    isValid = false;
  }

  // Add to table if all checks pass
  if (isValid) {
    addStudentRow(studentId, prefix, firstName, middleName, lastName, suffix, email, course, major, yearLevel);
    
    statusMsg.textContent = 'Student enrolled successfully!';
    statusMsg.classList.add('success');
    
    form.reset();
    majorBox.style.display = 'none';
  }
});

// Helper function to set error style and text
function setError(fieldId, msg) {
  const input = document.getElementById(fieldId);
  const errSpan = document.getElementById('err-' + fieldId);
  input.classList.add('invalid');
  if (errSpan) errSpan.textContent = msg;
}

// Helper function to clear error style and text
function clearError(fieldId) {
  const input = document.getElementById(fieldId);
  const errSpan = document.getElementById('err-' + fieldId);
  input.classList.remove('invalid');
  if (errSpan) errSpan.textContent = '';
}

// Append data to table
function addStudentRow(id, pre, fn, mn, ln, suf, email, course, major, year) {
  const tbody = document.querySelector('#recordsTable tbody');
  
  // Format full name organically
  let fullName = `${fn} ${ln}`;
  if (mn) fullName = `${fn} ${mn} ${ln}`;
  if (pre) fullName = `${pre} ${fullName}`;
  if (suf) fullName = `${fullName}, ${suf}`;

  let courseText = course;
  if (course === 'BSIT' && major) {
    courseText += ` (${major})`;
  }

  const row = document.createElement('tr');
  row.innerHTML = `
    <td>${id}</td>
    <td>${fullName}</td>
    <td>${email}</td>
    <td>${courseText}</td>
    <td>${year}</td>
  `;
  
  tbody.appendChild(row);
}