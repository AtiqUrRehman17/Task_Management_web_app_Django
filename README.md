# Task Manager — Django Web Application

A full-featured, user-authenticated task management web application built with Django. Users can register, log in, and manage their personal tasks and categories with filtering, search, and priority tracking.

---

## Table of Contents

- [Task Manager — Django Web Application](#task-manager--django-web-application)
  - [Table of Contents](#table-of-contents)
  - [Features](#features)
  - [Tech Stack](#tech-stack)
  - [Project Structure](#project-structure)
  - [Data Models](#data-models)
    - [`Category`](#category)
    - [`Task`](#task)
  - [URL Routes](#url-routes)
    - [Authentication](#authentication)
    - [Tasks](#tasks)
      - [Supported Query Parameters for `task_list`:](#supported-query-parameters-for-task_list)
    - [Categories](#categories)
  - [Getting Started](#getting-started)
    - [Prerequisites](#prerequisites)
    - [Installation](#installation)
  - [Usage Guide](#usage-guide)
    - [Registration \& Login](#registration--login)
    - [Managing Tasks](#managing-tasks)
    - [Managing Categories](#managing-categories)
  - [Form Details](#form-details)
    - [`TaskForm`](#taskform)
    - [`CategoryForm`](#categoryform)
  - [Admin Interface](#admin-interface)
  - [Future Improvements](#future-improvements)
  - [License](#license)

---

## Features

- **User Authentication** — Register, log in, and log out securely using Django's built-in auth system.
- **Task Management (CRUD)** — Create, view, update, and delete tasks.
- **Category Management (CRUD)** — Organize tasks into user-defined categories.
- **Task Filtering** — Filter tasks by status, priority, or category using query parameters.
- **Search** — Search tasks by title or description using a case-insensitive keyword search.
- **Overdue Detection** — Tasks automatically flag as overdue if their due date has passed and they are not completed.
- **User Isolation** — Each user only sees and manages their own tasks and categories.
- **Flash Messages** — Informative success/error feedback messages on every action.
- **Bootstrap-Ready Forms** — All form widgets use `form-control` CSS classes for seamless Bootstrap integration.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Backend Framework | Django (Python) |
| Database | SQLite (via `db.sqlite3`) |
| Frontend | Django Templates (HTML) |
| Authentication | Django's built-in `auth` module |
| Forms | Django ModelForms |
| ORM | Django ORM |

---

## Project Structure

```
taskmanager_project/
│
├── manage.py                        # Django management script
├── db.sqlite3                       # SQLite database file
├── requirements.txt                 # Project dependencies

│
├── taskmanager_project/             # Main project configuration
│   ├── __init__.py
│   ├── settings.py                  # Project settings
│   ├── urls.py                      # Main URL routing
│   └── wsgi.py                      # WSGI configuration
│
└── tasks/                            # Main application
    ├── __init__.py
    ├── admin.py                     # Admin interface registrations
    ├── apps.py                      # App configuration
    ├── models.py                    # Database models (Task, Category)
    ├── views.py                     # View controllers
    ├── urls.py                      # App URL routing
    ├── forms.py                     # ModelForm definitions
    │
    └── templates/
        └── task/
            ├── base.html                    # Base template (layout)
            ├── login.html                   # Login page
            ├── register.html                # Registration page
            ├── task_list.html               # Task listing with filters
            ├── task_form.html               # Task create/edit form
            ├── task_confirm_delete.html     # Task delete confirmation
            ├── category_list.html           # Category listing
            ├── category_form.html           # Category create/edit form
            └── category_confirm_delete.html # Category delete confirmation
```

---

## Data Models

### `Category`

Represents a user-defined label for grouping tasks.

| Field | Type | Description |
|---|---|---|
| `name` | `CharField(100)` | Unique category name |
| `description` | `TextField` | Optional description |
| `created_by` | `ForeignKey(User)` | Owner of the category |
| `created_at` | `DateTimeField` | Auto-set on creation |
| `updated_at` | `DateTimeField` | Auto-updated on save |

- Ordered alphabetically by name.
- Deletion of the owning user cascades to delete their categories.

---

### `Task`

The core model representing a to-do item.

| Field | Type | Description |
|---|---|---|
| `title` | `CharField(200)` | Task title |
| `description` | `TextField` | Optional description |
| `status` | `CharField` | `pending`, `in_progress`, or `completed` |
| `priority` | `CharField` | `low`, `medium`, or `high` |
| `due_date` | `DateField` | Optional due date |
| `created_at` | `DateTimeField` | Auto-set on creation |
| `updated_at` | `DateTimeField` | Auto-updated on save |
| `category` | `ForeignKey(Category)` | Optional linked category (nullable) |
| `user` | `ForeignKey(User)` | Owner of the task |

- Ordered by most recently created (`-created_at`).
- If a category is deleted, the task's category field is set to `NULL` (not deleted).
- The `is_overdue()` method returns `True` if `due_date` is in the past and the task is not completed.

---

## URL Routes

### Authentication

| Method | URL | View | Name |
|---|---|---|---|
| GET/POST | `/register/` | `register_view` | `register` |
| GET/POST | `/login/` | `login_view` | `login` |
| GET | `/logout/` | `logout_view` | `logout` |

### Tasks

| Method | URL | View | Name |
|---|---|---|---|
| GET | `/` | `task_list` | `task_list` |
| GET/POST | `/task/create/` | `task_create` | `task_create` |
| GET/POST | `/task/<pk>/update/` | `task_update` | `task_update` |
| GET/POST | `/task/<pk>/delete/` | `task_delete` | `task_delete` |

#### Supported Query Parameters for `task_list`:

- `?status=pending` — Filter by status (`pending`, `in_progress`, `completed`)
- `?priority=high` — Filter by priority (`low`, `medium`, `high`)
- `?category=<id>` — Filter by category ID
- `?search=keyword` — Search task titles and descriptions

### Categories

| Method | URL | View | Name |
|---|---|---|---|
| GET | `/categories/` | `category_list` | `category_list` |
| GET/POST | `/category/create/` | `category_create` | `category_create` |
| GET/POST | `/category/<pk>/update/` | `category_update` | `category_update` |
| GET/POST | `/category/<pk>/delete/` | `category_delete` | `category_delete` |

All task and category views require the user to be logged in (`@login_required`).

---

## Getting Started

### Prerequisites

- Python 3.8+
- pip

### Installation

**1. Clone the repository:**

```bash
git clone https://github.com/your-username/taskmanager_project.git
cd taskmanager_project
```

**2. Create and activate a virtual environment:**

```bash
python -m venv venv
source venv/bin/activate        # On Windows: venv\Scripts\activate
```

**3. Install dependencies:**

```bash
pip install -r requirements.txt
```

**4. Apply database migrations:**

```bash
python manage.py migrate
```

**5. (Optional) Create a superuser for admin access:**

```bash
python manage.py createsuperuser
```

**6. Start the development server:**

```bash
python manage.py runserver
```

**7. Open the app in your browser:**

```
http://127.0.0.1:8000/
```

---

## Usage Guide

### Registration & Login

Navigate to `/register/` to create a new account. After registration, you will be automatically logged in and redirected to your task list. Use `/login/` to return on future visits.

### Managing Tasks

From the task list (`/`), you can:

- Click **Create Task** to add a new task.
- Use the filter bar to narrow tasks by status, priority, or category.
- Use the search box to find tasks by title or description.
- Click **Edit** next to any task to update it.
- Click **Delete** to remove a task (a confirmation page is shown first).

### Managing Categories

Navigate to `/categories/` to view your categories. From there you can create, edit, and delete categories. Categories you create are available as options when creating or editing tasks.

> Note: Deleting a category does not delete associated tasks — it simply unlinks them.

---

## Form Details

### `TaskForm`

Used for both creating and editing tasks. Rendered with Bootstrap-compatible widgets.

Fields: `title`, `description`, `status`, `priority`, `due_date`, `category`

The category dropdown is dynamically filtered to only show categories belonging to the currently logged-in user.

### `CategoryForm`

Used for both creating and editing categories.

Fields: `name`, `description`

---

## Admin Interface

Both `Task` and `Category` models are registered with Django's built-in admin panel.

Access the admin interface at:

```
http://127.0.0.1:8000/admin/
```

Log in with your superuser credentials to view, create, edit, and delete all tasks and categories across all users.

---

## Future Improvements

- Add task due-date reminders via email notifications.
- Implement drag-and-drop task reordering.
- Add a dashboard with task statistics and completion charts.
- Support file attachments on tasks.
- Add REST API endpoints using Django REST Framework.
- Deploy to a production environment (e.g., Heroku, Railway, or AWS).

---

## License

This project is open source and available under the [MIT License](LICENSE).