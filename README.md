# Aws-Capstone-Project

## Front Pages
### 0. Base site description
This design for CareerConnect features a clean, modern, and professional look tailored for a student career platform. It uses a deep blue header with a prominent site name, a subtle tagline, and a responsive horizontal navigation bar that shifts to a vertical layout on smaller screens. The main content area is centered on a light background with soft shadows and rounded corners, creating a polished, approachable feel. The footer balances the design with a dark theme, highlighted links, and clear copyright info. Accent colors like gold on hover provide visual interest while maintaining a sophisticated, trust-building aesthetic—perfect for showcasing portfolios and job listings.

### 1. Main Site/Home Site
### 2. Portfolio form 
### 3. Jobs Listing Page

Navigation
1. User creates his portfolio by filling my the details
2. Can open the job listing page and can see the avail






## Database Schema
```sql
-- USERS TABLE: stores both students & recruiters
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    role ENUM('student', 'recruiter') NOT NULL,
    linkedin_url VARCHAR(255)
);

-- PORTFOLIOS TABLE: stores student profiles
CREATE TABLE portfolios (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    summary TEXT,
    skills TEXT,
    resume_s3_url VARCHAR(255),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);

-- JOB LISTINGS TABLE: stores jobs posted by recruiters
CREATE TABLE jobs (
    id INT AUTO_INCREMENT PRIMARY KEY,
    recruiter_id INT NOT NULL,
    title VARCHAR(100),
    description TEXT,
    required_skills TEXT,
    posted_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (recruiter_id) REFERENCES users(id) ON DELETE CASCADE
);

-- APPLICATIONS TABLE: keeps track of who applied to which job
CREATE TABLE applications (
    id INT AUTO_INCREMENT PRIMARY KEY,
    job_id INT NOT NULL,
    student_id INT NOT NULL,
    status ENUM('applied', 'shortlisted', 'rejected') DEFAULT 'applied',
    applied_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (job_id) REFERENCES jobs(id) ON DELETE CASCADE,
    FOREIGN KEY (student_id) REFERENCES users(id) ON DELETE CASCADE
);
```
# Flask Models
```python
class User(db.Model):
    __tablename__ = 'users'
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable=False)
    email = db.Column(db.String(100), unique=True, nullable=False)
    password_hash = db.Column(db.String(255), nullable=False)
    role = db.Column(db.Enum('student', 'recruiter'), nullable=False)
    linkedin_url = db.Column(db.String(255))

    portfolios = db.relationship('Portfolio', backref='user', cascade="all, delete-orphan")
    jobs = db.relationship('Job', backref='recruiter', cascade="all, delete-orphan")
    applications = db.relationship('Application', backref='student', cascade="all, delete-orphan", foreign_keys='Application.student_id')


class Portfolio(db.Model):
    __tablename__ = 'portfolios'
    id = db.Column(db.Integer, primary_key=True)
    user_id = db.Column(db.Integer, db.ForeignKey('users.id'), nullable=False)
    summary = db.Column(db.Text)
    skills = db.Column(db.Text)
    resume_s3_url = db.Column(db.String(255))
    created_at = db.Column(db.DateTime, default=datetime.utcnow)


class Job(db.Model):
    __tablename__ = 'jobs'
    id = db.Column(db.Integer, primary_key=True)
    recruiter_id = db.Column(db.Integer, db.ForeignKey('users.id'), nullable=False)
    title = db.Column(db.String(100))
    description = db.Column(db.Text)
    required_skills = db.Column(db.Text)
    posted_at = db.Column(db.DateTime, default=datetime.utcnow)

    applications = db.relationship('Application', backref='job', cascade="all, delete-orphan")


class Application(db.Model):
    __tablename__ = 'applications'
    id = db.Column(db.Integer, primary_key=True)
    job_id = db.Column(db.Integer, db.ForeignKey('jobs.id'), nullable=False)
    student_id = db.Column(db.Integer, db.ForeignKey('users.id'), nullable=False)
    status = db.Column(db.Enum('applied', 'shortlisted', 'rejected'), default='applied')
    applied_at = db.Column(db.DateTime, default=datetime.utcnow)
```
