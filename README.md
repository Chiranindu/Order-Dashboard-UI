# Order Dashboard

A Vue 3 based interactive order management dashboard that allows users to:
- Display list of orders
- Filter orders by multiple fields (e.g., Status, Type, Customer, etc.)
- Sort table columns (Ascending/Descending)
- Paginate results
- Save & load custom filter configurations
- Deploy as a single-page application (SPA)

---

# Features

- ✅ Multi-column filtering (checkbox-based)
- ✅ Column sorting with visual indicators
- ✅ Filter presets: Save & load configurations
- ✅ Pagination with customizable items per page
- ✅ Responsive and performant across all browsers
- ✅ Clean, minimal design using Tailwind CSS

---

# Tech Stack

- **Vue 3**
- **Tailwind CSS** – for styling
- **Vite** – fast development & bundling
- **Vercel / Netlify** – for deployment
- **Mock data provided via `mockOrders.json`**

---

# Project Structure

├── public/
├── src/
│   ├── components/
│   │   └── OrderFilters.vue     # Filter UI logic
│   ├── data/
│   │   └── mockOrders.json      # Provided mock data
│   ├── views/
│   │   └── OrderDashboard.vue   # Main app logic
│   └── App.vue
├── package.json
├── vite.config.js
└── README.md  # Project documentation

# Project Setup Instructions

1. Clone the repository
git clone "https://github.com/your-username/order-dashboard.git"
cd order-dashboard

2. Install dependencies
npm install

3. Start the development server
npm run dev

4. Build for production
npm run build

# Deployment

After running npm run build, deploy the contents of the dist/ folder to your hosting service (e.g., GitHub Pages, Vercel, Netlify, etc.).