# Task Master

Task Master is a browser-based workflow app for task tracking, daily work updates, admin-managed invitations, and login links.

## GitHub Pages Deployment

1. Create a new GitHub repository.
2. Upload these files to the repository root:
   - `index.html`
   - `.nojekyll`
   - `README.md`
3. Open the repository on GitHub.
4. Go to `Settings` -> `Pages`.
5. Under `Build and deployment`, choose:
   - Source: `Deploy from a branch`
   - Branch: `main`
   - Folder: `/root`
6. Save.
7. GitHub will give you a public link like:
   `https://your-username.github.io/your-repository-name/`

After publishing, paste that public link into the app Admin page under `App link users should open`.

## Default Admin Login

User ID: `TM-ADMIN`

Password: `Admin@123`

## Important

This version stores app data in each browser's local storage. GitHub Pages gives everyone a public link to open the app, but a shared database is needed later if all users should see the exact same live tasks and users automatically.
