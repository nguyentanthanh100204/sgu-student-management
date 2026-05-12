# 📚 API DOCUMENTATION - STUDENT MANAGEMENT SYSTEM

## 🎯 TỔNG QUAN

Backend APIs đã hoàn thành cho Student Management System, bao gồm 3 endpoints chính:

1. **Student List API** - Danh sách sinh viên với pagination, search, filters
2. **Transcript API** - Bảng điểm chi tiết theo học kỳ
3. **Graduation Status API** - Tình trạng tốt nghiệp

**Base URL:** `http://localhost:8081/api`

---

## 📋 API ENDPOINTS

### 1. GET /api/students - Student List

**Description:** Lấy danh sách sinh viên với pagination, search, và filters

**URL:** `GET http://localhost:8081/api/students`

**Query Parameters:**
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| page | Integer | No | 0 | Số trang (0-indexed) |
| size | Integer | No | 10 | Số lượng sinh viên mỗi trang |
| search | String | No | - | Tìm kiếm theo MSSV hoặc tên |
| className | String | No | - | Lọc theo lớp |
| departmentId | Long | No | - | Lọc theo khoa (ID) |
| course | Integer | No | - | Lọc theo khóa (năm) |

**Response:**
```json
{
  "students": [
    {
      "id": 2,
      "studentCode": "3122410001",
      "name": "Nguyễn Văn An",
      "email": "an.nv@student.sgu.edu.vn",
      "className": "DCT122C1",
      "departmentName": "Công nghệ thông tin",
      "departmentCode": "CNTT",
      "course": 2022,
      "totalCredits": 48,
      "gpa": 3.57,
      "academicStanding": "Giỏi",
      "status": "ACTIVE",
      "atRisk": false,
      "certificateCount": 4,
      "hasRequiredCertificates": true
    }
  ],
  "pagination": {
    "currentPage": 0,
    "pageSize": 10,
    "totalElements": 19,
    "totalPages": 2,
    "hasNext": true,
    "hasPrevious": false
  }
}
```

**Examples:**
```bash
# Get all students (page 0, size 10)
curl http://localhost:8081/api/students?page=0&size=10

# Search by student code
curl http://localhost:8081/api/students?search=3122410001

# Search by name
curl http://localhost:8081/api/students?search=Nguyen

# Filter by department
curl http://localhost:8081/api/students?departmentId=1

# Combine filters
curl http://localhost:8081/api/students?departmentId=1&page=0&size=5
```

**Academic Standing Mapping:**
| GPA Range | Academic Standing |
|-----------|-------------------|
| ≥ 3.6 | Xuất sắc |
| ≥ 3.2 | Giỏi |
| ≥ 2.5 | Khá |
| ≥ 2.0 | Trung bình |
| < 2.0 | Yếu |
| = 0.0 | Chưa có điểm |

---

### 2. GET /api/students/{id}/transcript - Student Transcript

**Description:** Lấy bảng điểm chi tiết của sinh viên theo học kỳ

**URL:** `GET http://localhost:8081/api/students/{id}/transcript`

**Path Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | Long | Yes | Student ID |

**Response:**
```json
{
  "studentId": 2,
  "studentCode": "3122410001",
  "studentName": "Nguyễn Văn An",
  "email": "an.nv@student.sgu.edu.vn",
  "departmentName": "Công nghệ thông tin",
  "className": "DCT122C1",
  "totalCredits": 48,
  "totalCourses": 16,
  "cumulativeGpa": 3.57,
  "academicStanding": "Giỏi",
  "semesters": [
    {
      "termId": 1,
      "termName": "HK1",
      "year": 2022,
      "totalCredits": 10,
      "semesterGpa": 3.64,
      "courses": [
        {
          "courseId": 1,
          "courseCode": "GE101",
          "courseName": "Triết học Mác - Lênin",
          "credits": 3,
          "midtermScore": 8.00,
          "finalScore": 8.50,
          "totalScore": 8.30,
          "gradeLetter": "B+",
          "gpaPoint": 3.50,
          "passed": true,
          "attemptNumber": 1
        }
      ]
    }
  ]
}
```

**Examples:**
```bash
# Get transcript for student ID 2
curl http://localhost:8081/api/students/2/transcript

# Get transcript for student ID 5
curl http://localhost:8081/api/students/5/transcript
```

**Grade Letter Mapping:**
| Grade | GPA Point | Score Range |
|-------|-----------|-------------|
| A+ | 4.0 | 9.5 - 10.0 |
| A | 3.7 | 8.5 - 9.4 |
| B+ | 3.5 | 8.0 - 8.4 |
| B | 3.0 | 7.0 - 7.9 |
| C+ | 2.5 | 6.5 - 6.9 |
| C | 2.0 | 5.5 - 6.4 |
| D+ | 1.5 | 5.0 - 5.4 |
| D | 1.0 | 4.0 - 4.9 |
| F | 0.0 | < 4.0 |

---

### 3. GET /api/students/graduation-status - Graduation Status

**Description:** Lấy tình trạng tốt nghiệp của tất cả sinh viên

**URL:** `GET http://localhost:8081/api/students/graduation-status`

**Query Parameters:**
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| eligibleOnly | Boolean | No | false | Chỉ trả về sinh viên đủ điều kiện |

**Response:**
```json
{
  "totalStudents": 19,
  "eligibleStudents": 0,
  "notEligibleStudents": 19,
  "eligibilityRate": 0.0,
  "candidates": [
    {
      "studentId": 3,
      "studentCode": "3122410002",
      "studentName": "Trần Thị Bình",
      "email": "binh.tt@student.sgu.edu.vn",
      "departmentName": "Công nghệ thông tin",
      "className": "DCT122C1",
      "programType": "CU_NHAN",
      "totalCredits": 60,
      "requiredCredits": 132,
      "gpa": 3.0,
      "academicStanding": "Khá",
      "certificateCount": 3,
      "hasEnglishCertificate": false,
      "hasITCertificate": true,
      "hasRequiredCertificates": false,
      "isEligible": false,
      "readinessPercentage": 90.15,
      "missingRequirements": [
        "Thiếu 72 tín chỉ",
        "Chưa có chứng chỉ Ngoại ngữ"
      ],
      "expectedGraduationYear": 2026,
      "expectedGraduationTerm": "HK2"
    }
  ]
}
```

**Examples:**
```bash
# Get all students graduation status
curl http://localhost:8081/api/students/graduation-status

# Get only eligible students
curl http://localhost:8081/api/students/graduation-status?eligibleOnly=true
```

**Graduation Requirements:**

**Cử nhân (Bachelor):**
- Credits: ≥ 132
- GPA: ≥ 2.0
- Certificates: 2 (ENGLISH + IT)

**Kỹ sư (Engineer):**
- Credits: ≥ 150
- GPA: ≥ 2.0
- Certificates: 2 (ENGLISH + IT)

**Readiness Percentage Calculation:**
```
Readiness = (Credits% + GPA% + Certificates%) / 3

Where:
- Credits% = min(100, totalCredits / requiredCredits * 100)
- GPA% = min(100, gpa / 4.0 * 100)
- Certificates% = hasRequired ? 100 : (count * 50)
```

---

## 🔧 ERROR HANDLING

All APIs return standard HTTP status codes:

| Status Code | Description |
|-------------|-------------|
| 200 | Success |
| 400 | Bad Request (invalid parameters) |
| 404 | Not Found (student not found) |
| 500 | Internal Server Error |

**Error Response Format:**
```json
{
  "timestamp": "2026-05-13T10:30:00",
  "status": 404,
  "error": "Not Found",
  "message": "Student not found with id: 999",
  "path": "/api/students/999/transcript"
}
```

---

## 🎯 USE CASES

### Use Case 1: Display Student List Page
```javascript
// Fetch students with pagination
fetch('http://localhost:8081/api/students?page=0&size=10')
  .then(response => response.json())
  .then(data => {
    console.log('Total students:', data.pagination.totalElements);
    data.students.forEach(student => {
      console.log(`${student.studentCode} - ${student.name} - GPA: ${student.gpa}`);
    });
  });
```

### Use Case 2: Search Students
```javascript
// Search by student code or name
const searchTerm = '3122410001';
fetch(`http://localhost:8081/api/students?search=${searchTerm}`)
  .then(response => response.json())
  .then(data => {
    console.log('Found:', data.pagination.totalElements, 'students');
  });
```

### Use Case 3: Display Student Transcript
```javascript
// Get transcript for student ID 2
fetch('http://localhost:8081/api/students/2/transcript')
  .then(response => response.json())
  .then(data => {
    console.log('Student:', data.studentName);
    console.log('Cumulative GPA:', data.cumulativeGpa);
    console.log('Total Credits:', data.totalCredits);
    
    data.semesters.forEach(semester => {
      console.log(`\n${semester.termName} ${semester.year} - GPA: ${semester.semesterGpa}`);
      semester.courses.forEach(course => {
        console.log(`  ${course.courseCode}: ${course.gradeLetter} (${course.totalScore})`);
      });
    });
  });
```

### Use Case 4: Check Graduation Eligibility
```javascript
// Get graduation status
fetch('http://localhost:8081/api/students/graduation-status')
  .then(response => response.json())
  .then(data => {
    console.log('Eligibility Rate:', data.eligibilityRate + '%');
    console.log('Eligible Students:', data.eligibleStudents);
    
    // Find students close to graduation (readiness > 80%)
    const closeToGraduation = data.candidates.filter(c => c.readinessPercentage > 80);
    console.log('Close to graduation:', closeToGraduation.length);
  });
```

---

## 📊 DATA MODELS

### Student
- id: Long
- studentCode: String (MSSV)
- name: String
- email: String
- className: String
- departmentName: String
- departmentCode: String
- course: Integer (năm nhập học)
- totalCredits: Integer
- gpa: Double
- academicStanding: String
- status: String (ACTIVE, SUSPENDED, EXPELLED, GRADUATED)
- atRisk: Boolean (GPA < 2.0)
- certificateCount: Integer
- hasRequiredCertificates: Boolean

### Transcript
- studentInfo: Object
- totalCredits: Integer
- totalCourses: Integer
- cumulativeGpa: Double
- academicStanding: String
- semesters: Array of Semester

### Semester
- termId: Long
- termName: String (HK1, HK2)
- year: Integer
- totalCredits: Integer
- semesterGpa: Double
- courses: Array of Course

### Course Grade
- courseId: Long
- courseCode: String
- courseName: String
- credits: Integer
- midtermScore: BigDecimal
- finalScore: BigDecimal
- totalScore: BigDecimal
- gradeLetter: String
- gpaPoint: BigDecimal
- passed: Boolean
- attemptNumber: Integer

### Graduation Candidate
- studentInfo: Object
- programType: String (CU_NHAN, KY_SU)
- totalCredits: Integer
- requiredCredits: Integer
- gpa: Double
- academicStanding: String
- certificateInfo: Object
- isEligible: Boolean
- readinessPercentage: Double
- missingRequirements: Array of String
- expectedGraduationYear: Integer
- expectedGraduationTerm: String

---

## 🧪 TESTING

Test scripts are available:
- `test_student_list_api.ps1` - Test Student List API
- `test_transcript_api.ps1` - Test Transcript API
- `test_graduation_status_api.ps1` - Test Graduation Status API

Run all tests:
```powershell
cd D:\KTPM\nckh
.\test_student_list_api.ps1
.\test_transcript_api.ps1
.\test_graduation_status_api.ps1
```

---

## 📞 SUPPORT

For issues or questions:
1. Check Backend logs in IntelliJ IDEA
2. Verify Backend is running on port 8081
3. Check database connection
4. Review error messages in API responses

---

**Last Updated:** May 13, 2026
**Version:** 1.0.0
**Backend Port:** 8081
**Database:** MySQL (sgu_student_management)
