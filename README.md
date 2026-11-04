
# NexusBlog

NexusBlog is a full-stack blogging platform built with React and Flask. Readers can explore and read published posts, while editors and admins can create and manage content.

## Features

- Register and log in with JWT authentication.
- Browse published posts and categories.
- View individual posts and their comments.
- Like posts and view liked posts.
- Add comments to published posts.
- Create, edit, publish, and delete posts, subject to user role.
- Upload post images and editor images to Cloudinary.
- Manage user roles as an administrator.
- View recent audit events in the admin area.
- Save the reading list in the user's account.
- Responsive React interface with protected routes.

### Roles

- **Reader:** Browse posts, like posts, and comment.
- **Editor:** Create and manage their own posts and moderate comments on their posts.
- **Admin:** Manage user roles and access administrator features.

New registrations receive the `reader` role by default.

## Tech stack

- **Frontend:** React 19, Vite, Tailwind CSS, React Router, Axios
- **Backend:** Python, Flask, Flask-SQLAlchemy, Flask-JWT-Extended
- **Image storage:** Cloudinary
- **Database:** SQLAlchemy-supported database configured with `DATABASE_URL`

## Requirements

- Python 3
- Node.js and npm
- A database, such as SQLite for local development
- Cloudinary credentials to upload images

## Installation

### 1. Configure the backend

From the project root, create and activate a virtual environment.

**Windows PowerShell:**

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
```

**macOS or Linux:**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Install the backend packages:

```bash
pip install Flask Flask-Cors Flask-JWT-Extended Flask-SQLAlchemy python-dotenv cloudinary
```

Create a `.env` file in the project root:

```dotenv
SECRET_KEY=replace-with-a-long-random-secret
JWT_SECRET=replace-with-a-different-long-random-secret
DATABASE_URL=sqlite:///blogging_platform.db
FRONTEND_URL=http://localhost:5173

CLOUDINARY_CLOUD_NAME=
CLOUDINARY_API_KEY=
CLOUDINARY_API_SECRET=
```

Set your own secret values. Add Cloudinary credentials if you want to upload images.

For MySQL, install the PyMySQL driver:

```bash
pip install PyMySQL
```

Then use a SQLAlchemy connection URL in `.env`:

```dotenv
DATABASE_URL=mysql+pymysql://USERNAME:PASSWORD@127.0.0.1:3306/blogging_platform
```

Create the `blogging_platform` database in MySQL before starting the backend.

### 2. Start the backend

From the project root:

```bash
python backend/app.py
```

The API starts at `http://localhost:5000`. When the app starts, it creates database tables and adds default categories if the category table is empty.

### 3. Start the frontend

Open a second terminal:

```bash
cd frontend
npm install
npm run dev
```

Open the Vite URL shown in the terminal, usually `http://localhost:5173`.

The frontend uses `http://localhost:5000/api` as its default API URL. To point it at another backend, set `VITE_API_URL` in the frontend environment, for example:

```dotenv
VITE_API_URL=https://your-api.example.com/api
```

## First administrator

New users are created with the `reader` role. To set up the first administrator, register an account, then update that user's role in the database:

```sql
UPDATE users
SET role = 'admin'
WHERE username = 'your_username';
```

Log out and log in again so the new role is included in the JWT.

## API overview

| Method | Endpoint | Purpose |
| --- | --- | --- |
| `GET` | `/health` | Check whether the API is running |
| `POST` | `/api/auth/register` | Register a reader account |
| `POST` | `/api/auth/login` | Log in and receive a JWT |
| `GET` | `/api/posts` | Get published posts |
| `GET` | `/api/posts/<slug>` | Get a published post |
| `GET` | `/api/posts/categories` | Get available categories |
| `POST` | `/api/posts` | Create a post (editor/admin) |
| `GET` | `/api/posts/my_posts` | Get the current creator's posts |
| `POST` | `/api/posts/<id>/like` | Like or unlike a post |
| `GET` | `/api/posts/liked` | Get the current user's liked posts |
| `POST` | `/api/comments/` | Add a comment |
| `GET` | `/api/comments/post/<post_id>` | Get approved comments |
| `GET` | `/api/admin/audit` | Get recent audit events (admin) |

Protected endpoints require the JWT access token in the request header:

```text
Authorization: Bearer YOUR_ACCESS_TOKEN
```

## Project structure

```text
Blogging_platform/
├── backend/
│   ├── app.py
│   ├── config.py
│   ├── middleware.py
│   ├── models.py
│   └── routes/
└── frontend/
    ├── src/
    │   ├── components/
    │   ├── context/
    │   ├── pages/
    │   └── services/
    ├── package.json
    └── vite.config.js
```

## Implementation notes

- The Admin Settings screen currently saves settings in browser local storage; it does not persist them to the backend.
- The comment moderation page calls pending/approve API endpoints that are not currently implemented. The backend currently provides a comment flagging endpoint instead.
- The Flask app runs with debug mode enabled for local development. Disable debug mode and use a production WSGI server before deploying publicly.

## License

No `LICENSE` file is currently included. Add one if you want to specify how others may use or distribute this project.
```
