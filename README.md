# SpaceX Landing Pads Dashboard

A SvelteKit application that displays information about SpaceX landing pads with interactive features and visualizations.

## Features

- View landing pad data in both list and grid layouts
- Interactive map showing landing pad locations
- Success rate visualization with donut chart
- Filter landing pads by status (Active, Retired, Under Construction)
- Detailed view for each landing pad
- Responsive design for all screen sizes

## Technologies Used

- SvelteKit 5
- TailwindCSS
- Flowbite Svelte Components
- OpenLayers for Maps
- Chart.js for Data Visualization
- SpaceX API

## Prerequisites

- Node.js (version 16 or higher)
- npm (comes with Node.js)

## Installation

1. Clone the repository:
```bash
git clone [repository-url]
cd assesmentsoftwrd
```

2. Install dependencies:
```bash
npm install
```

3. Start the development server:
```bash
npm run dev
```

4. Open your browser and navigate to `http://localhost:5173`

## Building for Production

To create a production build:

```bash
npm run build
```

The build files will be located in the `build` directory.

## Project Structure

```
src/
├── lib/
│   ├── components/
│   │   ├── SpaceXLandingPads.svelte
│   │   ├── LandingPadsMap.svelte
│   │   └── LandingPadsChart.svelte
│   └── stores/
│       └── filterStore.js
├── App.svelte
│ 
└── app.css
```

## API Integration

The application uses the SpaceX API to fetch landing pad data:
- Endpoint: `https://api.spacexdata.com/v3/landpads`
- No authentication required
- Data includes location coordinates, success rates, and status information

## Features in Detail

### List/Grid View
- Toggle between table and card layouts
- Comprehensive information display
- Success rate progress bars
- Status indicators with color coding

### Interactive Map
- Shows all landing pad locations
- Color-coded markers based on status
- Zoom and pan controls
- Responsive design

### Success Rate Chart
- Donut chart visualization
- Shows distribution of landing pad statuses
- Interactive legend
- Center display showing total count

### Filtering
- Filter by operational status
- Real-time updates across all views
- Clear visual indicators for active filters

## Contributing

1. Fork the repository
2. Create your feature branch: `git checkout -b feature/YourFeature`
3. Commit your changes: `git commit -am 'Add some feature'`
4. Push to the branch: `git push origin feature/YourFeature`
5. Submit a pull request

