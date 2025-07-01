# Aws-Capstone-Project

```pgsql
START
|
|--> Student registers (name, email, role=student)
|
|--> Fills profile (summary, skills)
|
|--> Uploads resume file
|      -> Flask uploads to S3
|      -> saves S3 URL in DB
|
|--> Recruiter logs in (role=recruiter)
|
|--> Views list of all student portfolios
|      -> sees name, skills, download resume link
|
END

```


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
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100),
    role ENUM('student', 'recruiter')
);

CREATE TABLE portfolios (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    summary TEXT,
    skills TEXT,
    resume_s3_url VARCHAR(255),
    FOREIGN KEY (user_id) REFERENCES users(id)
);

```
# Flask Models
```python
class User(db.Model):
    __tablename__ = 'users'
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable=False)
    email = db.Column(db.String(100), unique=True, nullable=False)
    role = db.Column(db.Enum('student', 'recruiter'), nullable=False)

    portfolios = db.relationship('Portfolio', backref='user', cascade="all, delete-orphan")


class Portfolio(db.Model):
    __tablename__ = 'portfolios'
    id = db.Column(db.Integer, primary_key=True)
    user_id = db.Column(db.Integer, db.ForeignKey('users.id'), nullable=False)
    summary = db.Column(db.Text)
    skills = db.Column(db.Text)
    resume_s3_url = db.Column(db.String(255))
```
