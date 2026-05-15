
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>IDEBATE Tournament Management</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/xlsx@0.18.5/dist/xlsx.full.min.js"></script>
    <style>
        :root {
            --primary: #007bff;
            --secondary: #6c757d;
            --success: #28a745;
            --info: #17a2b8;
            --warning: #ffc107;
            --danger: #dc3545;
            --light: #f8f9fa;
            --dark: #343a40;
            --teal: #20c997;
            --purple: #6f42c1;
            --orange: #ff7a00;
            --black: #111;
            --white: #fff;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: "Poppins", sans-serif;
            background-color: var(--light);
            overflow-x: hidden;
        }

        /* Loading Screen */
        #loading-screen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(135deg, var(--black) 0%, #333 100%);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 9999;
            color: white;
        }

        .loading-spinner {
            width: 50px;
            height: 50px;
            border: 5px solid rgba(0, 123, 255, 0.3);
            border-top: 5px solid var(--primary);
            border-radius: 50%;
            animation: spin 1s linear infinite;
            margin-bottom: 20px;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        /* Sidebar */
        .sidebar {
            position: fixed;
            top: 0;
            left: 0;
            width: 260px;
            height: 100vh;
            background: linear-gradient(180deg, var(--dark) 0%, #222 100%);
            color: var(--white);
            display: flex;
            flex-direction: column;
            padding-top: 20px;
            z-index: 1000;
            overflow-y: auto;
            box-shadow: 2px 0 10px rgba(0,0,0,0.1);
        }

        .sidebar-header {
            text-align: center;
            padding: 0 20px 20px;
            border-bottom: 1px solid #444;
            margin-bottom: 20px;
        }

        .sidebar img {
            width: 80px;
            margin-bottom: 10px;
        }

        .sidebar h4 {
            color: var(--primary);
            margin-bottom: 5px;
            text-transform: uppercase;
            font-weight: bold;
            font-size: 1.1rem;
        }

        .nav-link {
            color: var(--white) !important;
            padding: 12px 25px;
            border-bottom: 1px solid #444;
            transition: all 0.3s;
            text-transform: uppercase;
            font-weight: 500;
            display: flex;
            align-items: center;
            text-decoration: none;
            cursor: pointer;
            font-size: 0.9rem;
        }

        .nav-link i {
            width: 20px;
            margin-right: 12px;
            font-size: 1rem;
        }

        .nav-link.active,
        .nav-link:hover {
            background: linear-gradient(90deg, var(--primary) 0%, #3399ff 100%);
            color: var(--white) !important;
            border-left: 4px solid var(--white);
        }

        /* Main content */
        .main {
            margin-left: 260px;
            padding: 20px;
            min-height: 100vh;
        }

        .page {
            display: none;
        }

        .page.active {
            display: block;
        }

        .topbar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            background: linear-gradient(135deg, var(--white) 0%, #f8f9fa 100%);
            padding: 15px 25px;
            border-radius: 12px;
            margin-bottom: 25px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.08);
            border: 1px solid #e9ecef;
        }

        .topbar h2 {
            margin: 0;
            color: var(--dark);
            font-weight: 700;
            font-size: 1.5rem;
        }

        .btn-add {
            background: linear-gradient(135deg, var(--primary) 0%, #3399ff 100%);
            border: none;
            color: var(--white);
            font-weight: 600;
            padding: 10px 20px;
            border-radius: 8px;
            transition: all 0.3s;
            box-shadow: 0 2px 8px rgba(0, 123, 255, 0.3);
        }

        .btn-add:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 15px rgba(0, 123, 255, 0.4);
        }

        /* Tournament Progress */
        .tournament-progress {
            background: var(--white);
            border-radius: 12px;
            padding: 25px;
            margin-bottom: 25px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.08);
        }

        .progress-stage {
            margin-bottom: 30px;
        }

        .stage-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            padding-bottom: 15px;
            border-bottom: 2px solid #e9ecef;
        }

        .stage-title {
            font-weight: 700;
            color: var(--primary);
            font-size: 1.3rem;
        }

        .stage-count {
            background: var(--primary);
            color: white;
            border-radius: 20px;
            padding: 5px 15px;
            font-size: 0.9rem;
            font-weight: 600;
        }

        .matches-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(350px, 1fr));
            gap: 20px;
        }

        .match-card {
            background: linear-gradient(135deg, #f8f9fa 0%, var(--white) 100%);
            border-radius: 10px;
            padding: 20px;
            border: 2px solid #e9ecef;
            transition: all 0.3s;
        }

        .match-card:hover {
            border-color: var(--primary);
            transform: translateY(-3px);
            box-shadow: 0 6px 20px rgba(0,0,0,0.1);
        }

        .match-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
            padding-bottom: 10px;
            border-bottom: 1px solid #e9ecef;
        }

        .match-id {
            font-weight: 600;
            color: var(--dark);
        }

        .match-status {
            padding: 4px 12px;
            border-radius: 15px;
            font-size: 0.8rem;
            font-weight: 600;
        }

        .status-scheduled { background: #fff3cd; color: #856404; }
        .status-in-progress { background: #cce7ff; color: #004085; }
        .status-completed { background: #d4edda; color: #155724; }

        .teams-container {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
        }

        .team {
            text-align: center;
            flex: 1;
        }

        .team-name {
            font-weight: 600;
            margin-bottom: 5px;
        }

        .team-score {
            font-size: 1.5rem;
            font-weight: 700;
            color: var(--primary);
        }

        .vs {
            margin: 0 15px;
            font-weight: 700;
            color: #6c757d;
        }

        .match-actions {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
        }

        /* Toast Notifications */
        .toast-container {
            position: fixed;
            top: 20px;
            right: 20px;
            z-index: 9999;
            min-width: 300px;
        }

        .toast {
            border-radius: 8px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
        }

        /* Footer */
        .footer {
            background: var(--dark);
            color: var(--white);
            padding: 15px 25px;
            text-align: center;
            margin-top: 50px;
        }

        .footer a {
            color: var(--primary);
            text-decoration: none;
            font-weight: 600;
        }

        .footer a:hover {
            text-decoration: underline;
        }

        /* Results Cards */
        .results-container {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }

        .result-card {
            background-color: var(--white);
            border-radius: 8px;
            padding: 20px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.05);
            transition: transform 0.3s, box-shadow 0.3s;
            border-left: 4px solid var(--primary);
        }

        .result-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }

        .result-card h5 {
            color: var(--primary);
            margin-bottom: 15px;
            font-weight: 600;
        }

        .result-card .score {
            font-size: 24px;
            font-weight: 700;
            color: var(--dark);
        }

        .result-card .score-label {
            font-size: 14px;
            color: #666;
        }

        /* Speaker List */
        .speaker-list {
            background-color: var(--white);
            border-radius: 8px;
            overflow: hidden;
            box-shadow: 0 2px 10px rgba(0,0,0,0.05);
        }

        .speaker-list-header {
            background-color: var(--dark);
            color: var(--white);
            padding: 15px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .speaker-list-header h3 {
            margin: 0;
            font-size: 18px;
            font-weight: 600;
        }

        .speaker-list li {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 15px 20px;
            border-bottom: 1px solid #e9ecef;
            transition: background 0.2s;
            cursor: pointer;
        }

        .speaker-list li:hover {
            background-color: rgba(0, 123, 255, 0.05);
        }

        .speaker-list li:last-child {
            border-bottom: none;
        }

        .speaker-list li:nth-child(1) { border-left: 4px solid gold; }
        .speaker-list li:nth-child(2) { border-left: 4px solid silver; }
        .speaker-list li:nth-child(3) { border-left: 4px solid #cd7f32; }
        .speaker-list li:nth-child(n+4) { border-left: 4px solid #e9ecef; }

        .speaker-rank {
            display: flex;
            align-items: center;
            width: 40px;
            font-weight: 600;
            color: var(--dark);
        }

        .speaker-info {
            flex: 1;
            margin-left: 15px;
        }

        .speaker-name {
            font-weight: 500;
            margin-bottom: 3px;
        }

        .speaker-team {
            font-size: 12px;
            color: #666;
        }

        .speaker-score {
            font-weight: 600;
            color: var(--dark);
        }

        .speaker-medal {
            margin-left: 10px;
            font-size: 16px;
        }

        /* Analytics */
        .analytics-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 20px;
            margin-bottom: 25px;
        }

        .analytics-card {
            background: var(--white);
            border-radius: 12px;
            padding: 20px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.08);
            border-left: 4px solid var(--primary);
        }

        .analytics-card h5 {
            color: var(--primary);
            margin-bottom: 15px;
            font-weight: 600;
        }

        .issue-list {
            max-height: 300px;
            overflow-y: auto;
        }

        .issue-item {
            padding: 12px;
            border: 1px solid #e9ecef;
            border-radius: 8px;
            margin-bottom: 10px;
            cursor: pointer;
            transition: all 0.3s;
        }

        .issue-item:hover {
            border-color: var(--primary);
            background: #f0f8ff;
        }

        .issue-severity {
            display: inline-block;
            padding: 3px 8px;
            border-radius: 12px;
            font-size: 0.8rem;
            font-weight: 600;
            margin-right: 8px;
        }

        .severity-high { background: #f8d7da; color: #721c24; }
        .severity-medium { background: #fff3cd; color: #856404; }
        .severity-low { background: #d1ecf1; color: #0c5460; }

        /* Settings */
        .settings-section {
            background: var(--white);
            border-radius: 12px;
            padding: 25px;
            margin-bottom: 25px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.08);
        }

        .settings-section h4 {
            color: var(--primary);
            margin-bottom: 20px;
            font-weight: 600;
        }

        /* Search Bar */
        .search-container {
            position: relative;
            margin-bottom: 20px;
        }

        .search-input {
            padding-left: 40px;
            border-radius: 20px;
        }

        .search-icon {
            position: absolute;
            left: 15px;
            top: 50%;
            transform: translateY(-50%);
            color: #6c757d;
        }

        /* Score Input */
        .score-input {
            max-width: 80px;
            text-align: center;
        }

        .round-scores {
            display: flex;
            gap: 10px;
            margin-top: 10px;
        }

        .round-score {
            flex: 1;
            text-align: center;
            padding: 5px;
            border: 1px solid #ddd;
            border-radius: 5px;
        }

        .round-label {
            font-size: 0.8rem;
            color: #666;
        }

        .round-value {
            font-weight: bold;
            color: var(--primary);
        }

        /* Team Badges */
        .team-badge {
            display: inline-block;
            padding: 4px 8px;
            border-radius: 12px;
            font-size: 0.7rem;
            font-weight: 600;
            margin-left: 5px;
        }

        .badge-winner { background: gold; color: #000; }
        .badge-eliminated { background: #6c757d; color: white; }
        .badge-active { background: var(--success); color: white; }
        .badge-champion { background: linear-gradient(135deg, #ffd700, #ffed4e); color: #000; }

        /* Excel Upload */
        .upload-area {
            border: 2px dashed #ddd;
            border-radius: 10px;
            padding: 30px;
            text-align: center;
            background: #f9f9f9;
            transition: all 0.3s;
            cursor: pointer;
            margin-bottom: 20px;
        }

        .upload-area:hover {
            border-color: var(--primary);
            background: #f0f8ff;
        }

        .upload-area.dragover {
            border-color: var(--primary);
            background: #f0f8ff;
        }

        .upload-icon {
            font-size: 48px;
            color: var(--primary);
            margin-bottom: 15px;
        }

        .upload-text {
            margin-bottom: 15px;
        }

        .file-input {
            display: none;
        }

        .paste-area {
            border: 1px solid #ddd;
            border-radius: 5px;
            padding: 15px;
            background: #f9f9f9;
            margin-bottom: 20px;
        }

        .paste-textarea {
            width: 100%;
            min-height: 120px;
            border: 1px solid #ddd;
            border-radius: 5px;
            padding: 10px;
            font-family: monospace;
            resize: vertical;
        }

        /* School Badge */
        .school-badge {
            background: linear-gradient(135deg, var(--success) 0%, #20c997 100%);
            color: white;
            padding: 3px 8px;
            border-radius: 12px;
            font-size: 0.7rem;
            font-weight: 600;
            margin-left: 5px;
        }

        /* Level Controls */
        .level-controls {
            display: flex;
            gap: 5px;
            margin-top: 10px;
        }

        .level-btn {
            padding: 2px 8px;
            font-size: 0.7rem;
            border-radius: 10px;
        }

        /* Trend Indicators */
        .trend-up { color: var(--success); }
        .trend-down { color: var(--danger); }
        .trend-stable { color: var(--secondary); }

        /* Responsive */
        @media (max-width: 768px) {
            .sidebar {
                width: 100%;
                height: auto;
                position: relative;
            }
            
            .main {
                margin-left: 0;
            }
            
            .matches-grid {
                grid-template-columns: 1fr;
            }
            
            .results-container {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <!-- Loading Screen -->
    <div id="loading-screen">
        <div class="loading-spinner"></div>
        <div class="loading-text">Loading IDEBATE Tournament Manager</div>
    </div>

    <!-- Toast Container -->
    <div class="toast-container"></div>

    <!-- Sidebar -->
    <div class="sidebar">
        <div class="sidebar-header">
            <img src="https://i.ibb.co/VpbWwK7h/mr-logo1.jpg" alt="IDEBATE Logo">
            <h4>IDEBATE</h4>
            <div class="version">v3.0-MKB</div>
        </div>
        
        <a class="nav-link active" data-page="tournament">
            <i class="fas fa-chess-board"></i><span>Tournament Progress</span>
        </a>
        <a class="nav-link" data-page="results">
            <i class="fas fa-trophy"></i><span>Results</span>
        </a>
        <a class="nav-link" data-page="speakers">
            <i class="fas fa-user"></i><span>Speakers</span>
        </a>
        <a class="nav-link" data-page="teams">
            <i class="fas fa-users"></i><span>Teams</span>
        </a>
        <a class="nav-link" data-page="schools">
            <i class="fas fa-school"></i><span>Schools</span>
        </a>
        <a class="nav-link" data-page="leaderboard">
            <i class="fas fa-chart-line"></i><span>Leaderboard</span>
        </a>
        <a class="nav-link" data-page="analytics">
            <i class="fas fa-robot"></i><span>AI Analytics</span>
        </a>
        <a class="nav-link" data-page="settings">
            <i class="fas fa-cog"></i><span>Settings</span>
        </a>
    </div>

    <!-- Main Content -->
    <div class="main">
        <!-- Tournament Progress Page -->
        <div class="page active" id="tournament-page">
            <div class="topbar">
                <div>
                    <h2><i class="fas fa-chess-board me-2"></i>Tournament Progress</h2>
                </div>
                <div>
                    <button class="btn btn-add me-2" data-bs-toggle="modal" data-bs-target="#addTeamModal">
                        <i class="fas fa-plus me-1"></i> Add Team
                    </button>
                    <button class="btn btn-add" data-bs-toggle="modal" data-bs-target="#uploadExcelModal">
                        <i class="fas fa-file-excel me-1"></i> Upload Excel
                    </button>
                </div>
            </div>

            <div class="tournament-progress">
                <!-- Stages will be populated dynamically -->
            </div>
        </div>

        <!-- Results Page -->
        <div class="page" id="results-page">
            <div class="topbar">
                <div>
                    <h2><i class="fas fa-trophy me-2"></i>Tournament Results</h2>
                </div>
            </div>

            <!-- Results Overview Cards -->
            <div class="results-container">
                <div class="result-card">
                    <h5>Total Participants</h5>
                    <div class="score" id="total-participants">0</div>
                    <div class="score-label">Speakers</div>
                    <div class="trend up">
                        <i class="fas fa-arrow-up me-1"></i> Active Tournament
                    </div>
                </div>
                <div class="result-card">
                    <h5>Teams Competing</h5>
                    <div class="score" id="total-teams">0</div>
                    <div class="score-label">Teams</div>
                    <div class="trend up">
                        <i class="fas fa-arrow-up me-1"></i> Registered
                    </div>
                </div>
                <div class="result-card">
                    <h5>Average Score</h5>
                    <div class="score" id="average-score">0.0</div>
                    <div class="score-label">Points</div>
                    <div class="trend">
                        <i class="fas fa-chart-line me-1"></i> Overall Performance
                    </div>
                </div>
                <div class="result-card">
                    <h5>Schools Represented</h5>
                    <div class="score" id="total-schools">0</div>
                    <div class="score-label">Schools</div>
                    <div class="trend up">
                        <i class="fas fa-school me-1"></i> Participating
                    </div>
                </div>
            </div>

            <!-- Speaker Rankings -->
            <div class="speaker-list">
                <div class="speaker-list-header">
                    <h3><i class="fas fa-medal me-2"></i>Top Speakers Ranking</h3>
                </div>
                <ul class="list-unstyled mb-0" id="top-speakers-list">
                    <!-- Top speakers will be populated here -->
                </ul>
            </div>
        </div>

        <!-- Speakers Page -->
        <div class="page" id="speakers-page">
            <div class="topbar">
                <div>
                    <h2><i class="fas fa-user me-2"></i>Speakers Management</h2>
                </div>
                <button class="btn btn-add" data-bs-toggle="modal" data-bs-target="#addSpeakerModal">
                    <i class="fas fa-plus me-1"></i> Add Speaker
                </button>
            </div>

            <div class="search-container">
                <i class="fas fa-search search-icon"></i>
                <input type="text" class="form-control search-input" id="speaker-search" placeholder="Search speakers...">
            </div>

            <div class="speaker-list">
                <div class="speaker-list-header">
                    <h3><i class="fas fa-users me-2"></i>All Speakers</h3>
                </div>
                <ul class="list-unstyled mb-0" id="all-speakers-list">
                    <!-- All speakers will be populated here -->
                </ul>
            </div>
        </div>

        <!-- Teams Page -->
        <div class="page" id="teams-page">
            <div class="topbar">
                <div>
                    <h2><i class="fas fa-users me-2"></i>Teams Management</h2>
                </div>
                <div>
                    <button class="btn btn-add me-2" data-bs-toggle="modal" data-bs-target="#addTeamModal">
                        <i class="fas fa-plus me-1"></i> Add Team
                    </button>
                    <button class="btn btn-add" data-bs-toggle="modal" data-bs-target="#uploadExcelModal">
                        <i class="fas fa-file-excel me-1"></i> Upload Excel
                    </button>
                </div>
            </div>

            <div class="search-container">
                <i class="fas fa-search search-icon"></i>
                <input type="text" class="form-control search-input" id="team-search" placeholder="Search teams...">
            </div>

            <div class="results-container" id="teams-container">
                <!-- Teams will be populated here -->
            </div>
        </div>

        <!-- Schools Page -->
        <div class="page" id="schools-page">
            <div class="topbar">
                <div>
                    <h2><i class="fas fa-school me-2"></i>Schools Leaderboard</h2>
                </div>
                <button class="btn btn-add" data-bs-toggle="modal" data-bs-target="#addSchoolModal">
                    <i class="fas fa-plus me-1"></i> Add School
                </button>
            </div>

            <div class="results-container" id="schools-container">
                <!-- Schools will be populated here -->
            </div>
        </div>

        <!-- Leaderboard Page -->
        <div class="page" id="leaderboard-page">
            <div class="topbar">
                <div>
                    <h2><i class="fas fa-chart-line me-2"></i>Tournament Leaderboard</h2>
                </div>
            </div>
            <div class="results-container" id="leaderboard-teams">
                <!-- Leaderboard teams will be populated here -->
            </div>
        </div>

        <!-- AI Analytics Page -->
        <div class="page" id="analytics-page">
            <div class="topbar">
                <div>
                    <h2><i class="fas fa-robot me-2"></i>AI Analytics & Insights</h2>
                </div>
                <button class="btn btn-add" id="refreshAnalyticsBtn">
                    <i class="fas fa-sync-alt me-1"></i> Refresh Analytics
                </button>
            </div>
            <!-- Analytics content will be populated here -->
        </div>

        <!-- Settings Page -->
        <div class="page" id="settings-page">
            <div class="topbar">
                <div>
                    <h2><i class="fas fa-cog me-2"></i>System Settings</h2>
                </div>
            </div>
            <!-- Settings content will be populated here -->
        </div>
    </div>

    <!-- Footer -->
    <div class="footer">
        <div class="container">
            <p>&copy; 2025 IDEBATE Tournament Management System. Made by <a href="https://mkbertrand.vercel.app/" target="_blank">M.K.BERTRAND</a></p>
        </div>
    </div>

    <!-- Modals -->
    <!-- Add Team Modal -->
    <div class="modal fade" id="addTeamModal" tabindex="-1">
        <div class="modal-dialog modal-lg">
            <div class="modal-content">
                <div class="modal-header bg-dark text-white">
                    <h5 class="modal-title">Add New Team</h5>
                    <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body">
                    <form id="teamForm">
                        <div class="row">
                            <div class="col-md-6">
                                <div class="mb-3">
                                    <label class="form-label">Team Name *</label>
                                    <input type="text" class="form-control" id="teamName" required>
                                </div>
                            </div>
                            <div class="col-md-6">
                                <div class="mb-3">
                                    <label class="form-label">School *</label>
                                    <select class="form-select" id="teamSchool" required>
                                        <option value="">Select School</option>
                                    </select>
                                </div>
                            </div>
                        </div>
                        
                        <div class="row">
                            <div class="col-md-6">
                                <div class="mb-3">
                                    <label class="form-label">Class *</label>
                                    <select class="form-select" id="teamClass" required>
                                        <option value="">Select Class</option>
                                        <option value="S1">S1</option>
                                        <option value="S2">S2</option>
                                        <option value="S3">S3</option>
                                        <option value="S4">S4</option>
                                        <option value="S5">S5</option>
                                        <option value="S6">S6</option>
                                    </select>
                                </div>
                            </div>
                            <div class="col-md-6">
                                <div class="mb-3" id="sectionContainer" style="display: none;">
                                    <label class="form-label">Section *</label>
                                    <select class="form-select" id="teamSection">
                                        <option value="">Select Section</option>
                                    </select>
                                </div>
                            </div>
                        </div>

                        <div class="mb-3">
                            <label class="form-label">Speakers (Minimum 3)</label>
                            <div id="speakersContainer">
                                <input type="text" class="form-control mb-2 speaker-name" placeholder="Speaker 1" required>
                                <input type="text" class="form-control mb-2 speaker-name" placeholder="Speaker 2" required>
                                <input type="text" class="form-control mb-2 speaker-name" placeholder="Speaker 3">
                            </div>
                            <button type="button" class="btn btn-sm btn-outline-dark" id="addSpeakerBtn">
                                <i class="fas fa-plus me-1"></i> Add Another Speaker
                            </button>
                        </div>
                    </form>
                </div>
                <div class="modal-footer">
                    <button class="btn btn-secondary" data-bs-dismiss="modal">Cancel</button>
                    <button class="btn btn-add" id="saveTeamBtn">Save Team</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Add School Modal -->
    <div class="modal fade" id="addSchoolModal" tabindex="-1">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header bg-dark text-white">
                    <h5 class="modal-title">Add New School</h5>
                    <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body">
                    <form id="schoolForm">
                        <div class="mb-3">
                            <label class="form-label">School Name *</label>
                            <input type="text" class="form-control" id="schoolName" placeholder="Enter school name" required>
                        </div>
                        <div class="mb-3">
                            <label class="form-label">School Code (Optional)</label>
                            <input type="text" class="form-control" id="schoolCode" placeholder="e.g., SCH001">
                        </div>
                    </form>
                </div>
                <div class="modal-footer">
                    <button class="btn btn-secondary" data-bs-dismiss="modal">Cancel</button>
                    <button class="btn btn-add" id="saveSchoolBtn">Save School</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Edit Team Modal -->
    <div class="modal fade" id="editTeamModal" tabindex="-1">
        <div class="modal-dialog modal-lg">
            <div class="modal-content">
                <div class="modal-header bg-dark text-white">
                    <h5 class="modal-title">Edit Team</h5>
                    <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body">
                    <form id="editTeamForm">
                        <input type="hidden" id="editTeamId">
                        <div class="row">
                            <div class="col-md-6">
                                <div class="mb-3">
                                    <label class="form-label">Team Name *</label>
                                    <input type="text" class="form-control" id="editTeamName" required>
                                </div>
                            </div>
                            <div class="col-md-6">
                                <div class="mb-3">
                                    <label class="form-label">School *</label>
                                    <select class="form-select" id="editTeamSchool" required>
                                        <option value="">Select School</option>
                                    </select>
                                </div>
                            </div>
                        </div>
                        
                        <div class="row">
                            <div class="col-md-6">
                                <div class="mb-3">
                                    <label class="form-label">Class *</label>
                                    <select class="form-select" id="editTeamClass" required>
                                        <option value="">Select Class</option>
                                        <option value="S1">S1</option>
                                        <option value="S2">S2</option>
                                        <option value="S3">S3</option>
                                        <option value="S4">S4</option>
                                        <option value="S5">S5</option>
                                        <option value="S6">S6</option>
                                    </select>
                                </div>
                            </div>
                            <div class="col-md-6">
                                <div class="mb-3" id="editSectionContainer" style="display: none;">
                                    <label class="form-label">Section *</label>
                                    <select class="form-select" id="editTeamSection">
                                        <option value="">Select Section</option>
                                    </select>
                                </div>
                            </div>
                        </div>

                        <div class="mb-3">
                            <label class="form-label">Current Stage</label>
                            <select class="form-select" id="editTeamStage">
                                <option value="octa">Octa-Finals</option>
                                <option value="quarter">Quarter-Finals</option>
                                <option value="semi">Semi-Finals</option>
                                <option value="final">Finals</option>
                                <option value="eliminated">Eliminated</option>
                            </select>
                        </div>

                        <div class="mb-3">
                            <label class="form-label">Speakers</label>
                            <div id="editSpeakersContainer">
                                <!-- Speakers will be populated here -->
                            </div>
                            <button type="button" class="btn btn-sm btn-outline-dark mt-2" id="addEditSpeakerBtn">
                                <i class="fas fa-plus me-1"></i> Add Another Speaker
                            </button>
                        </div>
                    </form>
                </div>
                <div class="modal-footer">
                    <button class="btn btn-secondary" data-bs-dismiss="modal">Cancel</button>
                    <button class="btn btn-danger me-auto" id="deleteTeamBtn">Delete Team</button>
                    <button class="btn btn-add" id="updateTeamBtn">Update Team</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Upload Excel Modal -->
    <div class="modal fade" id="uploadExcelModal" tabindex="-1">
        <div class="modal-dialog modal-lg">
            <div class="modal-content">
                <div class="modal-header bg-dark text-white">
                    <h5 class="modal-title">Upload Excel File</h5>
                    <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body">
                    <div class="upload-area" id="uploadArea">
                        <div class="upload-icon">
                            <i class="fas fa-file-excel"></i>
                        </div>
                        <div class="upload-text">
                            <h5>Drag & Drop Excel File Here</h5>
                            <p class="text-muted">Supported formats: .xlsx, .xls</p>
                        </div>
                        <button class="btn btn-add" id="browseFileBtn">
                            <i class="fas fa-folder-open me-1"></i> Browse Files
                        </button>
                        <input type="file" id="excelFileInput" class="file-input" accept=".xlsx, .xls">
                    </div>
                    
                    <div class="paste-area">
                        <h6>Or Paste Data Directly</h6>
                        <p class="text-muted">Format: Member Name | Class Team | School (e.g., MURINDANGABO KANYANA ARBELLA | S1A TEAM 1 | School Name)</p>
                        <textarea class="paste-textarea" id="pasteData" placeholder="Paste your data here..."></textarea>
                        <button class="btn btn-sm btn-outline-primary mt-2" id="parsePastedDataBtn">
                            <i class="fas fa-code me-1"></i> Parse Pasted Data
                        </button>
                    </div>
                    
                    <div id="previewData" class="mt-4" style="display: none;">
                        <h6>Preview of Data to Import</h6>
                        <div class="table-responsive">
                            <table class="table table-sm table-bordered" id="previewTable">
                                <thead>
                                    <tr>
                                        <th>Member Name</th>
                                        <th>Class & Team</th>
                                        <th>School</th>
                                    </tr>
                                </thead>
                                <tbody></tbody>
                            </table>
                        </div>
                        <div class="alert alert-info mt-2">
                            <i class="fas fa-info-circle me-2"></i>
                            <span id="previewStats">Found 0 teams with 0 members</span>
                        </div>
                    </div>
                </div>
                <div class="modal-footer">
                    <button class="btn btn-secondary" data-bs-dismiss="modal">Cancel</button>
                    <button class="btn btn-add" id="importDataBtn" disabled>
                        <i class="fas fa-upload me-1"></i> Import Data
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- Add Speaker Modal -->
    <div class="modal fade" id="addSpeakerModal" tabindex="-1">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header bg-dark text-white">
                    <h5 class="modal-title">Add New Speaker</h5>
                    <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body">
                    <form id="speakerForm">
                        <div class="mb-3">
                            <label class="form-label">Speaker Name</label>
                            <input type="text" class="form-control" id="speakerName" placeholder="Enter speaker name" required>
                        </div>

                        <div class="mb-3">
                            <label class="form-label">Team</label>
                            <select class="form-select" id="speakerTeam" required>
                                <option value="" disabled selected>Select Team</option>
                            </select>
                        </div>
                    </form>
                </div>
                <div class="modal-footer">
                    <button class="btn btn-secondary" data-bs-dismiss="modal">Cancel</button>
                    <button class="btn btn-add" id="saveSpeakerBtn">Save Speaker</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Edit Match Modal -->
    <div class="modal fade" id="editMatchModal" tabindex="-1">
        <div class="modal-dialog modal-lg">
            <div class="modal-content">
                <div class="modal-header bg-dark text-white">
                    <h5 class="modal-title">Edit Match Score</h5>
                    <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body">
                    <form id="matchForm">
                        <div id="matchTeamsInfo"></div>
                        <div class="row">
                            <div class="col-md-6">
                                <div class="mb-3">
                                    <label class="form-label" id="team1Label">Team 1 Score</label>
                                    <input type="number" class="form-control" id="team1Score" min="0" max="90" required>
                                </div>
                            </div>
                            <div class="col-md-6">
                                <div class="mb-3">
                                    <label class="form-label" id="team2Label">Team 2 Score</label>
                                    <input type="number" class="form-control" id="team2Score" min="0" max="90" required>
                                </div>
                            </div>
                        </div>
                        <div class="mb-3">
                            <label class="form-label">Status</label>
                            <select class="form-select" id="matchStatus">
                                <option value="scheduled">Scheduled</option>
                                <option value="in-progress">In Progress</option>
                                <option value="completed">Completed</option>
                            </select>
                        </div>
                    </form>
                </div>
                <div class="modal-footer">
                    <button class="btn btn-secondary" data-bs-dismiss="modal">Cancel</button>
                    <button class="btn btn-add" id="saveMatchBtn">Save Match</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Edit Speaker Scores Modal -->
    <div class="modal fade" id="editSpeakerScoresModal" tabindex="-1">
        <div class="modal-dialog modal-lg">
            <div class="modal-content">
                <div class="modal-header bg-dark text-white">
                    <h5 class="modal-title">Edit Speaker Scores</h5>
                    <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body">
                    <form id="speakerScoresForm">
                        <div id="speakerInfo"></div>
                        <div class="mb-3">
                            <h6>Round Scores (Max 30 per round)</h6>
                            <div class="round-scores">
                                <div class="round-score">
                                    <div class="round-label">Round 1</div>
                                    <input type="number" class="form-control score-input round1-score" min="0" max="30" value="0">
                                </div>
                                <div class="round-score">
                                    <div class="round-label">Round 2</div>
                                    <input type="number" class="form-control score-input round2-score" min="0" max="30" value="0">
                                </div>
                                <div class="round-score">
                                    <div class="round-label">Round 3</div>
                                    <input type="number" class="form-control score-input round3-score" min="0" max="30" value="0">
                                </div>
                            </div>
                        </div>
                        <div class="alert alert-info">
                            <strong>Total Score: <span id="currentTotalScore">0</span></strong> / 90
                        </div>
                    </form>
                </div>
                <div class="modal-footer">
                    <button class="btn btn-secondary" data-bs-dismiss="modal">Cancel</button>
                    <button class="btn btn-add" id="saveSpeakerScoresBtn">Save Scores</button>
                </div>
            </div>
        </div>
    </div>

    <!-- View Team Details Modal -->
    <div class="modal fade" id="viewTeamModal" tabindex="-1">
        <div class="modal-dialog modal-lg">
            <div class="modal-content">
                <div class="modal-header bg-dark text-white">
                    <h5 class="modal-title" id="viewTeamTitle">Team Details</h5>
                    <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body">
                    <div class="row mb-3">
                        <div class="col-md-4">
                            <strong>School:</strong> <span id="viewTeamSchool"></span>
                        </div>
                        <div class="col-md-4">
                            <strong>Class:</strong> <span id="viewTeamClass"></span>
                        </div>
                        <div class="col-md-4">
                            <strong>Total Score:</strong> <span id="viewTeamScore"></span>
                        </div>
                    </div>
                    <h6>Team Members & Scores:</h6>
                    <div id="viewTeamMembers">
                        <!-- Team members will be populated here -->
                    </div>
                </div>
                <div class="modal-footer">
                    <button class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
                </div>
            </div>
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>
    <script>
        // Enhanced Database with Unlimited Teams and School Support
        class TournamentDatabase {
            constructor() {
                this.key = 'idebate-tournament-data-v3';
                this.data = this.loadData();
            }

            loadData() {
                try {
                    const stored = localStorage.getItem(this.key);
                    if (stored) {
                        return JSON.parse(stored);
                    } else {
                        return this.getDefaultData();
                    }
                } catch (error) {
                    console.error('Error loading data:', error);
                    return this.getDefaultData();
                }
            }

            saveData() {
                try {
                    this.data.lastUpdated = new Date().toISOString();
                    localStorage.setItem(this.key, JSON.stringify(this.data));
                } catch (error) {
                    console.error('Error saving data:', error);
                }
            }

            getDefaultData() {
                return {
                    schools: [
                        { id: 1, name: "Default School", code: "SCH001", totalPoints: 0, teams: [] }
                    ],
                    teams: [],
                    speakers: [],
                    matches: {
                        octa: [],
                        quarter: [],
                        semi: [],
                        final: []
                    },
                    tournament: {
                        currentStage: 'octa',
                        stages: {
                            octa: { completed: false, winners: [] },
                            quarter: { completed: false, winners: [] },
                            semi: { completed: false, winners: [] },
                            final: { completed: false, winners: [] }
                        },
                        champion: null
                    },
                    settings: {
                        maxRounds: {
                            octa: 1,
                            quarter: 1,
                            semi: 1,
                            final: 1
                        },
                        maxScorePerRound: 30,
                        maxTotalScore: 90
                    },
                    lastUpdated: new Date().toISOString()
                };
            }

            // School methods
            addSchool(schoolData) {
                try {
                    const newSchool = {
                        id: this.data.schools.length > 0 ? Math.max(...this.data.schools.map(s => s.id)) + 1 : 1,
                        ...schoolData,
                        totalPoints: 0,
                        teams: [],
                        createdAt: new Date().toISOString()
                    };
                    this.data.schools.push(newSchool);
                    this.saveData();
                    return newSchool;
                } catch (error) {
                    console.error('Error adding school:', error);
                    throw error;
                }
            }

            getSchool(schoolId) {
                return this.data.schools.find(s => s.id === schoolId);
            }

            updateSchoolPoints(schoolId) {
                try {
                    const school = this.getSchool(schoolId);
                    if (school) {
                        const schoolTeams = this.data.teams.filter(t => t.schoolId === schoolId);
                        school.totalPoints = schoolTeams.reduce((sum, team) => sum + team.totalScore, 0);
                        school.teams = schoolTeams.map(t => t.id);
                        this.saveData();
                    }
                } catch (error) {
                    console.error('Error updating school points:', error);
                }
            }

            // Team methods - UNLIMITED TEAMS
            addTeam(teamData) {
                try {
                    const newTeam = {
                        id: this.data.teams.length > 0 ? Math.max(...this.data.teams.map(t => t.id)) + 1 : 1,
                        ...teamData,
                        speakers: [],
                        totalScore: 0,
                        status: 'active',
                        stage: 'octa', // Current stage
                        previousScores: [], // For trend tracking
                        wins: 0,
                        badges: [],
                        createdAt: new Date().toISOString()
                    };
                    this.data.teams.push(newTeam);
                    
                    // Update school teams list
                    const school = this.getSchool(teamData.schoolId);
                    if (school) {
                        school.teams.push(newTeam.id);
                        this.updateSchoolPoints(teamData.schoolId);
                    }
                    
                    this.saveData();
                    return newTeam;
                } catch (error) {
                    console.error('Error adding team:', error);
                    throw error;
                }
            }

            updateTeam(teamId, updates) {
                try {
                    const team = this.data.teams.find(t => t.id === teamId);
                    if (team) {
                        // Track score changes for trends
                        if (updates.totalScore !== undefined && updates.totalScore !== team.totalScore) {
                            team.previousScores.push({
                                score: team.totalScore,
                                timestamp: new Date().toISOString()
                            });
                            // Keep only last 5 scores for trend calculation
                            if (team.previousScores.length > 5) {
                                team.previousScores.shift();
                            }
                        }
                        
                        Object.assign(team, updates);
                        team.updatedAt = new Date().toISOString();
                        
                        // Update school points if team's school changed
                        if (updates.schoolId && updates.schoolId !== team.schoolId) {
                            this.updateSchoolPoints(team.schoolId); // Old school
                            this.updateSchoolPoints(updates.schoolId); // New school
                        } else {
                            this.updateSchoolPoints(team.schoolId);
                        }
                        
                        this.saveData();
                    }
                } catch (error) {
                    console.error('Error updating team:', error);
                }
            }

            deleteTeam(teamId) {
                try {
                    const team = this.data.teams.find(t => t.id === teamId);
                    if (team) {
                        // Remove from school
                        const school = this.getSchool(team.schoolId);
                        if (school) {
                            school.teams = school.teams.filter(id => id !== teamId);
                            this.updateSchoolPoints(team.schoolId);
                        }
                        
                        // Remove team
                        this.data.teams = this.data.teams.filter(t => t.id !== teamId);
                        
                        // Remove team's speakers
                        this.data.speakers = this.data.speakers.filter(s => s.teamId !== teamId);
                        
                        this.saveData();
                    }
                } catch (error) {
                    console.error('Error deleting team:', error);
                }
            }

            // Manual leveling for teams
            updateTeamStage(teamId, newStage) {
                try {
                    const team = this.data.teams.find(t => t.id === teamId);
                    if (team) {
                        team.stage = newStage;
                        team.updatedAt = new Date().toISOString();
                        this.saveData();
                    }
                } catch (error) {
                    console.error('Error updating team stage:', error);
                }
            }

            // Speaker methods
            addSpeaker(speakerData) {
                try {
                    const newSpeaker = {
                        id: this.data.speakers.length > 0 ? Math.max(...this.data.speakers.map(s => s.id)) + 1 : 1,
                        ...speakerData,
                        rounds: [0, 0, 0],
                        totalScore: 0,
                        previousScores: [],
                        createdAt: new Date().toISOString()
                    };
                    this.data.speakers.push(newSpeaker);
                    
                    // Update team's speaker list
                    const team = this.data.teams.find(t => t.id === speakerData.teamId);
                    if (team) {
                        team.speakers.push(newSpeaker.id);
                        this.updateTeamTotalScore(team.id);
                    }
                    
                    this.saveData();
                    return newSpeaker;
                } catch (error) {
                    console.error('Error adding speaker:', error);
                    throw error;
                }
            }

            updateSpeaker(speakerId, updates) {
                try {
                    const speaker = this.data.speakers.find(s => s.id === speakerId);
                    if (speaker) {
                        // Track score changes for trends
                        if (updates.totalScore !== undefined && updates.totalScore !== speaker.totalScore) {
                            speaker.previousScores.push({
                                score: speaker.totalScore,
                                timestamp: new Date().toISOString()
                            });
                            if (speaker.previousScores.length > 5) {
                                speaker.previousScores.shift();
                            }
                        }
                        
                        Object.assign(speaker, updates);
                        speaker.updatedAt = new Date().toISOString();
                        this.saveData();
                    }
                } catch (error) {
                    console.error('Error updating speaker:', error);
                }
            }

            deleteSpeaker(speakerId) {
                try {
                    const speaker = this.data.speakers.find(s => s.id === speakerId);
                    if (speaker) {
                        // Remove from team
                        const team = this.data.teams.find(t => t.id === speaker.teamId);
                        if (team) {
                            team.speakers = team.speakers.filter(id => id !== speakerId);
                            this.updateTeamTotalScore(team.id);
                        }
                        
                        // Remove speaker
                        this.data.speakers = this.data.speakers.filter(s => s.id !== speakerId);
                        this.saveData();
                    }
                } catch (error) {
                    console.error('Error deleting speaker:', error);
                }
            }

            updateSpeakerScores(speakerId, rounds) {
                try {
                    const speaker = this.data.speakers.find(s => s.id === speakerId);
                    if (speaker) {
                        // Track previous score for trends
                        speaker.previousScores.push({
                            score: speaker.totalScore,
                            timestamp: new Date().toISOString()
                        });
                        if (speaker.previousScores.length > 5) {
                            speaker.previousScores.shift();
                        }
                        
                        speaker.rounds = rounds;
                        speaker.totalScore = rounds.reduce((sum, score) => sum + score, 0);
                        speaker.updatedAt = new Date().toISOString();
                        
                        // Update team total score
                        const team = this.data.teams.find(t => t.speakers.includes(speakerId));
                        if (team) {
                            this.updateTeamTotalScore(team.id);
                        }
                        
                        this.saveData();
                    }
                } catch (error) {
                    console.error('Error updating speaker scores:', error);
                }
            }

            updateTeamTotalScore(teamId) {
                try {
                    const team = this.data.teams.find(t => t.id === teamId);
                    if (team) {
                        const teamSpeakers = this.data.speakers.filter(s => s.teamId === teamId);
                        const newTotalScore = teamSpeakers.reduce((sum, speaker) => sum + speaker.totalScore, 0);
                        
                        // Only update if score changed
                        if (newTotalScore !== team.totalScore) {
                            team.previousScores.push({
                                score: team.totalScore,
                                timestamp: new Date().toISOString()
                            });
                            if (team.previousScores.length > 5) {
                                team.previousScores.shift();
                            }
                            
                            team.totalScore = newTotalScore;
                            this.updateSchoolPoints(team.schoolId);
                            this.saveData();
                        }
                    }
                } catch (error) {
                    console.error('Error updating team total score:', error);
                }
            }

            // Enhanced ranking methods
            getSchoolRankings() {
                try {
                    return this.data.schools
                        .filter(school => school.teams.length > 0)
                        .sort((a, b) => b.totalPoints - a.totalPoints)
                        .map((school, index) => ({
                            ...school,
                            rank: index + 1,
                            trend: this.calculateTrend(school.previousScores || [])
                        }));
                } catch (error) {
                    console.error('Error getting school rankings:', error);
                    return [];
                }
            }

            getTeamRankings() {
                try {
                    return this.data.teams
                        .filter(team => team.status === 'active' || team.status === 'champion')
                        .sort((a, b) => b.totalScore - a.totalScore)
                        .map((team, index) => {
                            const school = this.getSchool(team.schoolId);
                            return {
                                ...team,
                                rank: index + 1,
                                schoolName: school ? school.name : 'Unknown School',
                                trend: this.calculateTrend(team.previousScores)
                            };
                        });
                } catch (error) {
                    console.error('Error getting team rankings:', error);
                    return [];
                }
            }

            getSpeakerRankings() {
                try {
                    return this.data.speakers
                        .sort((a, b) => b.totalScore - a.totalScore)
                        .map((speaker, index) => {
                            const team = this.data.teams.find(t => t.id === speaker.teamId);
                            const school = team ? this.getSchool(team.schoolId) : null;
                            return {
                                ...speaker,
                                rank: index + 1,
                                teamName: team ? team.name : 'No Team',
                                schoolName: school ? school.name : 'Unknown School',
                                trend: this.calculateTrend(speaker.previousScores),
                                medal: index < 3 ? ['gold', 'silver', 'bronze'][index] : null
                            };
                        });
                } catch (error) {
                    console.error('Error getting speaker rankings:', error);
                    return [];
                }
            }

            calculateTrend(previousScores) {
                try {
                    if (previousScores.length < 2) return 'stable';
                    
                    const recentScores = previousScores.slice(-3).map(s => s.score);
                    if (recentScores.length < 2) return 'stable';
                    
                    const current = recentScores[recentScores.length - 1];
                    const previous = recentScores[0];
                    
                    if (current > previous) return 'up';
                    if (current < previous) return 'down';
                    return 'stable';
                } catch (error) {
                    console.error('Error calculating trend:', error);
                    return 'stable';
                }
            }

            // Tournament methods
            autoArrangeMatches(stage) {
                try {
                    const teams = this.getTeamsForStage(stage);
                    const matches = [];
                    
                    // Shuffle teams and pair them
                    const shuffled = [...teams].sort(() => Math.random() - 0.5);
                    
                    for (let i = 0; i < shuffled.length; i += 2) {
                        if (i + 1 < shuffled.length) {
                            const match = {
                                id: matches.length + 1,
                                team1: shuffled[i].id,
                                team2: shuffled[i + 1].id,
                                scores: { team1: 0, team2: 0 },
                                status: 'scheduled',
                                winner: null,
                                rounds: [],
                                createdAt: new Date().toISOString()
                            };
                            matches.push(match);
                        }
                    }
                    
                    this.data.matches[stage] = matches;
                    this.saveData();
                    return matches;
                } catch (error) {
                    console.error('Error arranging matches:', error);
                    return [];
                }
            }

            getTeamsForStage(stage) {
                try {
                    // For manual leveling, use team.stage property
                    if (stage === 'octa') {
                        return this.data.teams.filter(team => team.stage === 'octa' && team.status === 'active');
                    } else {
                        return this.data.teams.filter(team => team.stage === stage);
                    }
                } catch (error) {
                    console.error('Error getting teams for stage:', error);
                    return [];
                }
            }

            updateMatchScore(stage, matchId, scores) {
                try {
                    const match = this.data.matches[stage].find(m => m.id === matchId);
                    if (match) {
                        match.scores = scores;
                        match.updatedAt = new Date().toISOString();
                        
                        // Determine winner
                        if (scores.team1 > scores.team2) {
                            match.winner = match.team1;
                        } else if (scores.team2 > scores.team1) {
                            match.winner = match.team2;
                        } else {
                            match.winner = null; // Draw
                        }
                        
                        match.status = 'completed';
                        this.saveData();
                    }
                } catch (error) {
                    console.error('Error updating match score:', error);
                }
            }

            proceedToNextStage(currentStage) {
                try {
                    const nextStage = this.getNextStage(currentStage);
                    if (!nextStage) return;
                    
                    // Mark current stage as completed
                    this.data.tournament.stages[currentStage].completed = true;
                    
                    // Collect winners for next stage
                    const winners = this.data.matches[currentStage]
                        .filter(match => match.winner)
                        .map(match => match.winner);
                    
                    this.data.tournament.stages[currentStage].winners = winners;
                    this.data.tournament.currentStage = nextStage;
                    
                    // Update team stages for winners
                    winners.forEach(teamId => {
                        this.updateTeamStage(teamId, nextStage);
                    });
                    
                    this.saveData();
                    return nextStage;
                } catch (error) {
                    console.error('Error proceeding to next stage:', error);
                    return null;
                }
            }

            getNextStage(currentStage) {
                const stages = ['octa', 'quarter', 'semi', 'final'];
                const currentIndex = stages.indexOf(currentStage);
                return stages[currentIndex + 1] || null;
            }

            declareChampion(teamId) {
                try {
                    this.data.tournament.champion = teamId;
                    this.data.tournament.stages.final.completed = true;
                    
                    const championTeam = this.data.teams.find(t => t.id === teamId);
                    if (championTeam) {
                        championTeam.badges.push('champion');
                        championTeam.status = 'champion';
                        championTeam.stage = 'champion';
                    }
                    
                    this.saveData();
                } catch (error) {
                    console.error('Error declaring champion:', error);
                }
            }

            // Utility methods
            getTeamSpeakers(teamId) {
                try {
                    return this.data.speakers.filter(s => s.teamId === teamId);
                } catch (error) {
                    console.error('Error getting team speakers:', error);
                    return [];
                }
            }

            getSpeakerTeam(speakerId) {
                try {
                    const speaker = this.data.speakers.find(s => s.id === speakerId);
                    return speaker ? this.data.teams.find(t => t.id === speaker.teamId) : null;
                } catch (error) {
                    console.error('Error getting speaker team:', error);
                    return null;
                }
            }

            calculateTeamStats() {
                try {
                    const totalParticipants = this.data.speakers.length;
                    const totalTeams = this.data.teams.length;
                    const totalSchools = this.data.schools.filter(s => s.teams.length > 0).length;
                    const activeTeams = this.data.teams.filter(t => t.status === 'active').length;
                    const averageScore = totalParticipants > 0 ? 
                        this.data.speakers.reduce((sum, speaker) => sum + speaker.totalScore, 0) / totalParticipants : 0;
                    
                    return {
                        totalParticipants,
                        totalTeams,
                        totalSchools,
                        activeTeams,
                        averageScore: averageScore.toFixed(1)
                    };
                } catch (error) {
                    console.error('Error calculating team stats:', error);
                    return {
                        totalParticipants: 0,
                        totalTeams: 0,
                        totalSchools: 0,
                        activeTeams: 0,
                        averageScore: 0
                    };
                }
            }

            clearData() {
                try {
                    this.data = this.getDefaultData();
                    this.saveData();
                } catch (error) {
                    console.error('Error clearing data:', error);
                }
            }

            // Enhanced Excel import with school support
            importTeamsFromExcelData(data) {
                try {
                    const teamsMap = {};
                    const schoolsMap = {};
                    
                    data.forEach(row => {
                        if (row.Members && row.Classes) {
                            const memberName = row.Members.trim();
                            const classTeam = row.Classes.trim();
                            const schoolName = row.School ? row.School.trim() : 'Default School';
                            
                            // Create school if it doesn't exist
                            if (!schoolsMap[schoolName]) {
                                const existingSchool = this.data.schools.find(s => 
                                    s.name.toLowerCase() === schoolName.toLowerCase()
                                );
                                
                                if (existingSchool) {
                                    schoolsMap[schoolName] = existingSchool.id;
                                } else {
                                    const newSchool = this.addSchool({
                                        name: schoolName,
                                        code: `SCH${(this.data.schools.length + 1).toString().padStart(3, '0')}`
                                    });
                                    schoolsMap[schoolName] = newSchool.id;
                                }
                            }
                            
                            // Parse class and team name
                            const teamMatch = classTeam.match(/(S[1-6])([A-Z]?)\s*(TEAM\s*\d+|.*)/i);
                            if (teamMatch) {
                                const classLevel = teamMatch[1];
                                const section = teamMatch[2] || '';
                                const teamName = teamMatch[3].trim();
                                
                                const fullTeamName = `${schoolName} - ${classLevel}${section} ${teamName}`;
                                
                                if (!teamsMap[fullTeamName]) {
                                    teamsMap[fullTeamName] = {
                                        name: fullTeamName,
                                        schoolId: schoolsMap[schoolName],
                                        class: classLevel,
                                        section: section,
                                        members: []
                                    };
                                }
                                
                                teamsMap[fullTeamName].members.push(memberName);
                            }
                        }
                    });
                    
                    // Create teams and speakers
                    Object.values(teamsMap).forEach(teamData => {
                        if (teamData.members.length >= 3) {
                            const newTeam = this.addTeam({
                                name: teamData.name,
                                schoolId: teamData.schoolId,
                                class: teamData.class,
                                section: teamData.section
                            });
                            
                            teamData.members.forEach(memberName => {
                                this.addSpeaker({
                                    name: memberName,
                                    teamId: newTeam.id
                                });
                            });
                        }
                    });
                    
                    return Object.values(teamsMap);
                } catch (error) {
                    console.error('Error importing teams from Excel:', error);
                    return [];
                }
            }

            // Analytics
            getAnalytics() {
                try {
                    const issues = [];
                    
                    // Check for teams without speakers
                    this.data.teams.forEach(team => {
                        if (team.speakers.length === 0) {
                            issues.push({
                                type: 'team_no_speakers',
                                severity: 'high',
                                message: `Team "${team.name}" has no speakers assigned`,
                                teamId: team.id,
                                action: 'add_speakers'
                            });
                        }
                    });
                    
                    // Check for speakers with incomplete scores
                    this.data.speakers.forEach(speaker => {
                        const hasMissingScores = speaker.rounds.some(score => score === 0);
                        if (hasMissingScores && this.data.tournament.currentStage === 'octa') {
                            const team = this.getSpeakerTeam(speaker.id);
                            issues.push({
                                type: 'speaker_missing_scores',
                                severity: 'medium',
                                message: `Speaker "${speaker.name}" has incomplete scores`,
                                speakerId: speaker.id,
                                action: 'update_scores'
                            });
                        }
                    });
                    
                    return issues;
                } catch (error) {
                    console.error('Error getting analytics:', error);
                    return [];
                }
            }
        }

        // Enhanced Main Application with New Features
        class IDEBATEApp {
            constructor() {
                this.database = new TournamentDatabase();
                this.currentPage = 'tournament';
                this.currentEditingMatch = null;
                this.currentEditingSpeaker = null;
                this.currentViewingTeam = null;
                this.currentEditingTeam = null;
                this.init();
            }

            init() {
                this.hideLoading();
                this.setupEventListeners();
                this.updateUI();
            }

            hideLoading() {
                try {
                    document.getElementById('loading-screen').style.display = 'none';
                } catch (error) {
                    console.error('Error hiding loading screen:', error);
                }
            }

            setupEventListeners() {
                try {
                    // Navigation
                    document.querySelectorAll('.nav-link').forEach(link => {
                        link.addEventListener('click', (e) => {
                            e.preventDefault();
                            const page = e.currentTarget.getAttribute('data-page');
                            this.showPage(page);
                            
                            document.querySelectorAll('.nav-link').forEach(l => l.classList.remove('active'));
                            e.currentTarget.classList.add('active');
                        });
                    });

                    // Tournament controls
                    document.getElementById('proceedQuarterBtn')?.addEventListener('click', () => this.proceedToNextStage('octa'));
                    document.getElementById('proceedSemiBtn')?.addEventListener('click', () => this.proceedToNextStage('quarter'));
                    document.getElementById('proceedFinalBtn')?.addEventListener('click', () => this.proceedToNextStage('semi'));
                    document.getElementById('declareChampionBtn')?.addEventListener('click', () => this.declareChampion());

                    // Team modal
                    document.getElementById('teamClass').addEventListener('change', (e) => this.handleClassChange(e.target.value));
                    document.getElementById('addSpeakerBtn').addEventListener('click', () => this.addSpeakerField());
                    document.getElementById('saveTeamBtn').addEventListener('click', () => this.saveTeam());

                    // School modal
                    document.getElementById('saveSchoolBtn').addEventListener('click', () => this.saveSchool());

                    // Edit team modal
                    document.getElementById('editTeamClass').addEventListener('change', (e) => this.handleEditClassChange(e.target.value));
                    document.getElementById('addEditSpeakerBtn').addEventListener('click', () => this.addEditSpeakerField());
                    document.getElementById('updateTeamBtn').addEventListener('click', () => this.updateTeam());
                    document.getElementById('deleteTeamBtn').addEventListener('click', () => this.deleteTeam());

                    // Excel upload modal
                    document.getElementById('browseFileBtn').addEventListener('click', () => document.getElementById('excelFileInput').click());
                    document.getElementById('excelFileInput').addEventListener('change', (e) => this.handleFileSelect(e));
                    document.getElementById('parsePastedDataBtn').addEventListener('click', () => this.parsePastedData());
                    document.getElementById('importDataBtn').addEventListener('click', () => this.importData());

                    // Drag and drop for file upload
                    const uploadArea = document.getElementById('uploadArea');
                    uploadArea.addEventListener('dragover', (e) => {
                        e.preventDefault();
                        uploadArea.classList.add('dragover');
                    });
                    uploadArea.addEventListener('dragleave', () => {
                        uploadArea.classList.remove('dragover');
                    });
                    uploadArea.addEventListener('drop', (e) => {
                        e.preventDefault();
                        uploadArea.classList.remove('dragover');
                        const files = e.dataTransfer.files;
                        if (files.length > 0) {
                            document.getElementById('excelFileInput').files = files;
                            this.handleFileSelect({ target: document.getElementById('excelFileInput') });
                        }
                    });

                    // Speaker modal
                    document.getElementById('saveSpeakerBtn').addEventListener('click', () => this.saveSpeaker());

                    // Match modal
                    document.getElementById('saveMatchBtn').addEventListener('click', () => this.saveMatch());

                    // Speaker scores modal
                    document.getElementById('saveSpeakerScoresBtn').addEventListener('click', () => this.saveSpeakerScores());

                    // Round score inputs
                    document.querySelectorAll('.round1-score, .round2-score, .round3-score').forEach(input => {
                        input.addEventListener('input', () => this.updateTotalScoreDisplay());
                    });

                    // Analytics
                    document.getElementById('refreshAnalyticsBtn').addEventListener('click', () => this.refreshAnalytics());

                    // Search functionality
                    document.getElementById('speaker-search')?.addEventListener('input', (e) => this.filterSpeakers(e.target.value));
                    document.getElementById('team-search')?.addEventListener('input', (e) => this.filterTeams(e.target.value));

                    // Global event delegation
                    document.addEventListener('click', (e) => {
                        if (e.target.closest('.edit-match-btn')) {
                            const matchId = parseInt(e.target.closest('.edit-match-btn').dataset.matchId);
                            const stage = e.target.closest('.edit-match-btn').dataset.stage;
                            this.editMatch(stage, matchId);
                        }
                        if (e.target.closest('.edit-speaker-scores-btn')) {
                            const speakerId = parseInt(e.target.closest('.edit-speaker-scores-btn').dataset.speakerId);
                            this.editSpeakerScores(speakerId);
                        }
                        if (e.target.closest('.view-team-btn')) {
                            const teamId = parseInt(e.target.closest('.view-team-btn').dataset.teamId);
                            this.viewTeam(teamId);
                        }
                        if (e.target.closest('.edit-team-btn')) {
                            const teamId = parseInt(e.target.closest('.edit-team-btn').dataset.teamId);
                            this.editTeam(teamId);
                        }
                        if (e.target.closest('.level-up-btn')) {
                            const teamId = parseInt(e.target.closest('.level-up-btn').dataset.teamId);
                            this.promoteTeam(teamId);
                        }
                        if (e.target.closest('.level-down-btn')) {
                            const teamId = parseInt(e.target.closest('.level-down-btn').dataset.teamId);
                            this.demoteTeam(teamId);
                        }
                        if (e.target.closest('.issue-item')) {
                            const issue = JSON.parse(e.target.closest('.issue-item').dataset.issue);
                            this.handleAnalyticsIssue(issue);
                        }
                        if (e.target.closest('.remove-speaker-btn')) {
                            const speakerId = parseInt(e.target.closest('.remove-speaker-btn').dataset.speakerId);
                            this.removeSpeakerFromEdit(speakerId);
                        }
                    });
                } catch (error) {
                    console.error('Error setting up event listeners:', error);
                }
            }

            showPage(page) {
                try {
                    this.currentPage = page;
                    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
                    document.getElementById(`${page}-page`).classList.add('active');
                    this.updateUI();
                } catch (error) {
                    console.error('Error showing page:', error);
                }
            }

            updateUI() {
                try {
                    this.updateTournamentProgress();
                    this.updateStageCounts();
                    this.updateProceedButtons();
                    
                    if (this.currentPage === 'results') this.updateResults();
                    if (this.currentPage === 'speakers') this.updateSpeakersList();
                    if (this.currentPage === 'teams') this.updateTeamsList();
                    if (this.currentPage === 'schools') this.updateSchoolsList();
                    if (this.currentPage === 'leaderboard') this.updateLeaderboard();
                    if (this.currentPage === 'analytics') this.updateAnalytics();
                    if (this.currentPage === 'settings') this.updateSettings();

                    // Update school selects in modals
                    this.updateSchoolSelects();
                } catch (error) {
                    console.error('Error updating UI:', error);
                }
            }

            updateSchoolSelects() {
                try {
                    const schoolSelects = [
                        document.getElementById('teamSchool'),
                        document.getElementById('editTeamSchool')
                    ];

                    schoolSelects.forEach(select => {
                        if (select) {
                            const currentValue = select.value;
                            select.innerHTML = '<option value="">Select School</option>';
                            
                            this.database.data.schools.forEach(school => {
                                const option = document.createElement('option');
                                option.value = school.id;
                                option.textContent = school.name;
                                select.appendChild(option);
                            });
                            
                            // Restore previous value if possible
                            if (currentValue) {
                                select.value = currentValue;
                            }
                        }
                    });
                } catch (error) {
                    console.error('Error updating school selects:', error);
                }
            }

            updateTournamentProgress() {
                try {
                    const container = document.querySelector('.tournament-progress');
                    if (!container) return;

                    const stages = [
                        { key: 'octa', name: 'Octa-Finals', description: 'All Teams - Knockout Round' },
                        { key: 'quarter', name: 'Quarter-Finals', description: 'Top Teams - Knockout Round' },
                        { key: 'semi', name: 'Semi-Finals', description: '4 Teams - Knockout Round' },
                        { key: 'final', name: 'Finals', description: '2 Teams - Championship' }
                    ];

                    let html = '';
                    stages.forEach(stage => {
                        const teams = this.database.getTeamsForStage(stage.key);
                        const matches = this.database.data.matches[stage.key];
                        
                        html += `
                            <div class="progress-stage">
                                <div class="stage-header">
                                    <div>
                                        <div class="stage-title">${stage.name}</div>
                                        <small class="text-muted">${stage.description}</small>
                                    </div>
                                    <div class="stage-count">${teams.length} teams</div>
                                </div>
                                <div class="matches-grid" id="${stage.key}-matches">
                        `;
                        
                        if (matches.length === 0 && teams.length > 0) {
                            html += `
                                <div class="text-center p-4">
                                    <p class="text-muted">No matches arranged yet</p>
                                    <button class="btn btn-add arrange-matches-btn" data-stage="${stage.key}">
                                        <i class="fas fa-random me-1"></i> Arrange Matches
                                    </button>
                                </div>
                            `;
                        } else if (matches.length === 0) {
                            html += `<div class="text-center p-4 text-muted">No teams in this stage</div>`;
                        } else {
                            matches.forEach(match => {
                                const team1 = this.database.data.teams.find(t => t.id === match.team1);
                                const team2 = this.database.data.teams.find(t => t.id === match.team2);
                                
                                if (team1 && team2) {
                                    html += this.createMatchCardHTML(match, team1, team2, stage.key);
                                }
                            });
                        }
                        
                        html += `
                                </div>
                                ${stage.key !== 'final' ? `
                                    <div class="text-center mt-3">
                                        <button class="btn btn-add" id="proceed${this.capitalize(stage.key)}Btn" ${stage.key === 'octa' ? '' : 'disabled'}>
                                            <i class="fas fa-arrow-right me-1"></i> Proceed to ${stages[stages.findIndex(s => s.key === stage.key) + 1]?.name}
                                        </button>
                                    </div>
                                ` : `
                                    <div class="text-center mt-3">
                                        <button class="btn btn-add" id="declareChampionBtn" disabled>
                                            <i class="fas fa-crown me-1"></i> Declare Champion
                                        </button>
                                    </div>
                                `}
                            </div>
                        `;
                    });

                    container.innerHTML = html;

                    // Re-attach event listeners
                    this.reattachEventListeners();
                } catch (error) {
                    console.error('Error updating tournament progress:', error);
                }
            }

            createMatchCardHTML(match, team1, team2, stage) {
                try {
                    const statusClass = `status-${match.status.replace(' ', '-')}`;
                    const team1Badges = team1.badges.map(badge => `<span class="team-badge badge-${badge}">${badge}</span>`).join('');
                    const team2Badges = team2.badges.map(badge => `<span class="team-badge badge-${badge}">${badge}</span>`).join('');
                    const team1School = this.database.getSchool(team1.schoolId);
                    const team2School = this.database.getSchool(team2.schoolId);
                    
                    return `
                        <div class="match-card">
                            <div class="match-header">
                                <span class="match-id">Match ${match.id}</span>
                                <span class="match-status ${statusClass}">${match.status}</span>
                            </div>
                            <div class="teams-container">
                                <div class="team">
                                    <div class="team-name">
                                        ${team1.name} ${team1Badges}
                                        <div class="school-badge">${team1School?.name || 'Unknown'}</div>
                                    </div>
                                    <div class="team-score">${match.scores.team1}</div>
                                </div>
                                <div class="vs">VS</div>
                                <div class="team">
                                    <div class="team-name">
                                        ${team2.name} ${team2Badges}
                                        <div class="school-badge">${team2School?.name || 'Unknown'}</div>
                                    </div>
                                    <div class="team-score">${match.scores.team2}</div>
                                </div>
                            </div>
                            <div class="match-actions">
                                <button class="btn btn-sm btn-outline-primary edit-match-btn" 
                                        data-match-id="${match.id}" data-stage="${stage}">
                                    <i class="fas fa-edit me-1"></i>Edit Score
                                </button>
                                ${match.winner ? `
                                    <span class="badge bg-success">
                                        Winner: ${this.database.data.teams.find(t => t.id === match.winner)?.name}
                                    </span>
                                ` : ''}
                            </div>
                        </div>
                    `;
                } catch (error) {
                    console.error('Error creating match card HTML:', error);
                    return '';
                }
            }

            reattachEventListeners() {
                try {
                    // Re-attach event listeners for dynamically created buttons
                    const stages = ['octa', 'quarter', 'semi'];
                    stages.forEach(stage => {
                        const button = document.getElementById(`proceed${this.capitalize(stage)}Btn`);
                        if (button) {
                            button.addEventListener('click', () => this.proceedToNextStage(stage));
                        }

                        const arrangeButton = document.querySelector(`.arrange-matches-btn[data-stage="${stage}"]`);
                        if (arrangeButton) {
                            arrangeButton.addEventListener('click', () => this.arrangeMatches(stage));
                        }
                    });

                    const championBtn = document.getElementById('declareChampionBtn');
                    if (championBtn) {
                        championBtn.addEventListener('click', () => this.declareChampion());
                    }
                } catch (error) {
                    console.error('Error reattaching event listeners:', error);
                }
            }

            arrangeMatches(stage) {
                try {
                    this.database.autoArrangeMatches(stage);
                    this.updateUI();
                    this.showToast(`Matches arranged for ${stage} stage!`, 'success');
                } catch (error) {
                    console.error('Error arranging matches:', error);
                    this.showToast('Error arranging matches', 'error');
                }
            }

            updateStageCounts() {
                try {
                    const stages = ['octa', 'quarter', 'semi', 'final'];
                    
                    stages.forEach(stage => {
                        const countElement = document.getElementById(`${stage}-count`);
                        if (countElement) {
                            const teams = this.database.getTeamsForStage(stage);
                            countElement.textContent = `${teams.length} teams`;
                        }
                    });
                } catch (error) {
                    console.error('Error updating stage counts:', error);
                }
            }

            updateProceedButtons() {
                try {
                    const stages = ['octa', 'quarter', 'semi'];
                    
                    stages.forEach(stage => {
                        const button = document.getElementById(`proceed${this.capitalize(stage)}Btn`);
                        if (button) {
                            const allMatchesCompleted = this.database.data.matches[stage].every(match => match.status === 'completed');
                            const hasWinners = this.database.data.matches[stage].some(match => match.winner);
                            const hasMatches = this.database.data.matches[stage].length > 0;
                            button.disabled = !(hasMatches && allMatchesCompleted && hasWinners);
                        }
                    });

                    // Final champion button
                    const championBtn = document.getElementById('declareChampionBtn');
                    if (championBtn) {
                        const finalMatch = this.database.data.matches.final[0];
                        championBtn.disabled = !(finalMatch && finalMatch.winner);
                    }
                } catch (error) {
                    console.error('Error updating proceed buttons:', error);
                }
            }

            proceedToNextStage(currentStage) {
                try {
                    const nextStage = this.database.proceedToNextStage(currentStage);
                    if (nextStage) {
                        this.database.autoArrangeMatches(nextStage);
                        this.updateUI();
                        this.showToast(`Proceeded to ${nextStage} stage successfully!`, 'success');
                    }
                } catch (error) {
                    console.error('Error proceeding to next stage:', error);
                    this.showToast('Error proceeding to next stage', 'error');
                }
            }

            declareChampion() {
                try {
                    const finalMatch = this.database.data.matches.final[0];
                    if (finalMatch && finalMatch.winner) {
                        this.database.declareChampion(finalMatch.winner);
                        this.updateUI();
                        
                        const championTeam = this.database.data.teams.find(t => t.id === finalMatch.winner);
                        this.showToast(`🏆 ${championTeam.name} has been declared the tournament champion!`, 'success');
                    }
                } catch (error) {
                    console.error('Error declaring champion:', error);
                    this.showToast('Error declaring champion', 'error');
                }
            }

            // Manual Leveling Methods
            promoteTeam(teamId) {
                try {
                    const team = this.database.data.teams.find(t => t.id === teamId);
                    if (!team) return;

                    const stages = ['octa', 'quarter', 'semi', 'final', 'champion'];
                    const currentIndex = stages.indexOf(team.stage);
                    
                    if (currentIndex < stages.length - 1) {
                        const newStage = stages[currentIndex + 1];
                        this.database.updateTeamStage(teamId, newStage);
                        this.updateUI();
                        this.showToast(`Team promoted to ${newStage} stage!`, 'success');
                    }
                } catch (error) {
                    console.error('Error promoting team:', error);
                    this.showToast('Error promoting team', 'error');
                }
            }

            demoteTeam(teamId) {
                try {
                    const team = this.database.data.teams.find(t => t.id === teamId);
                    if (!team) return;

                    const stages = ['octa', 'quarter', 'semi', 'final', 'champion'];
                    const currentIndex = stages.indexOf(team.stage);
                    
                    if (currentIndex > 0) {
                        const newStage = stages[currentIndex - 1];
                        this.database.updateTeamStage(teamId, newStage);
                        this.updateUI();
                        this.showToast(`Team demoted to ${newStage} stage!`, 'warning');
                    }
                } catch (error) {
                    console.error('Error demoting team:', error);
                    this.showToast('Error demoting team', 'error');
                }
            }

            editMatch(stage, matchId) {
                try {
                    const match = this.database.data.matches[stage].find(m => m.id === matchId);
                    if (!match) return;

                    const team1 = this.database.data.teams.find(t => t.id === match.team1);
                    const team2 = this.database.data.teams.find(t => t.id === match.team2);

                    document.getElementById('matchTeamsInfo').innerHTML = `
                        <div class="alert alert-info">
                            <strong>${team1.name}</strong> vs <strong>${team2.name}</strong>
                            <br><small>Stage: ${this.formatStageName(stage)}</small>
                        </div>
                    `;

                    document.getElementById('team1Label').textContent = `${team1.name} Score`;
                    document.getElementById('team2Label').textContent = `${team2.name} Score`;
                    document.getElementById('team1Score').value = match.scores.team1;
                    document.getElementById('team2Score').value = match.scores.team2;
                    document.getElementById('matchStatus').value = match.status;

                    this.currentEditingMatch = { stage, matchId };

                    const modal = new bootstrap.Modal(document.getElementById('editMatchModal'));
                    modal.show();
                } catch (error) {
                    console.error('Error editing match:', error);
                    this.showToast('Error editing match', 'error');
                }
            }

            saveMatch() {
                try {
                    if (!this.currentEditingMatch) return;

                    const { stage, matchId } = this.currentEditingMatch;
                    const team1Score = parseInt(document.getElementById('team1Score').value);
                    const team2Score = parseInt(document.getElementById('team2Score').value);
                    const status = document.getElementById('matchStatus').value;

                    this.database.updateMatchScore(stage, matchId, {
                        team1: team1Score,
                        team2: team2Score
                    });

                    // Update match status
                    const match = this.database.data.matches[stage].find(m => m.id === matchId);
                    if (match) {
                        match.status = status;
                        this.database.saveData();
                    }

                    this.updateUI();
                    bootstrap.Modal.getInstance(document.getElementById('editMatchModal')).hide();
                    this.currentEditingMatch = null;

                    this.showToast('Match scores updated successfully!', 'success');
                } catch (error) {
                    console.error('Error saving match:', error);
                    this.showToast('Error saving match', 'error');
                }
            }

            editSpeakerScores(speakerId) {
                try {
                    const speaker = this.database.data.speakers.find(s => s.id === speakerId);
                    if (!speaker) return;

                    const team = this.database.getSpeakerTeam(speakerId);

                    document.getElementById('speakerInfo').innerHTML = `
                        <div class="alert alert-info">
                            <strong>${speaker.name}</strong> - ${team?.name || 'No Team'}
                            <br><small>Edit scores for all 3 rounds (max 30 per round)</small>
                        </div>
                    `;

                    // Set current round scores
                    document.querySelector('.round1-score').value = speaker.rounds[0] || 0;
                    document.querySelector('.round2-score').value = speaker.rounds[1] || 0;
                    document.querySelector('.round3-score').value = speaker.rounds[2] || 0;

                    this.updateTotalScoreDisplay();
                    this.currentEditingSpeaker = speakerId;

                    const modal = new bootstrap.Modal(document.getElementById('editSpeakerScoresModal'));
                    modal.show();
                } catch (error) {
                    console.error('Error editing speaker scores:', error);
                    this.showToast('Error editing speaker scores', 'error');
                }
            }

            updateTotalScoreDisplay() {
                try {
                    const round1 = parseInt(document.querySelector('.round1-score').value) || 0;
                    const round2 = parseInt(document.querySelector('.round2-score').value) || 0;
                    const round3 = parseInt(document.querySelector('.round3-score').value) || 0;
                    const total = round1 + round2 + round3;
                    
                    document.getElementById('currentTotalScore').textContent = total;
                    
                    // Highlight if over maximum
                    if (total > 90) {
                        document.getElementById('currentTotalScore').style.color = 'red';
                    } else {
                        document.getElementById('currentTotalScore').style.color = 'inherit';
                    }
                } catch (error) {
                    console.error('Error updating total score display:', error);
                }
            }

            saveSpeakerScores() {
                try {
                    if (!this.currentEditingSpeaker) return;

                    const round1 = Math.min(30, parseInt(document.querySelector('.round1-score').value) || 0);
                    const round2 = Math.min(30, parseInt(document.querySelector('.round2-score').value) || 0);
                    const round3 = Math.min(30, parseInt(document.querySelector('.round3-score').value) || 0);

                    this.database.updateSpeakerScores(this.currentEditingSpeaker, [round1, round2, round3]);
                    
                    this.updateUI();
                    bootstrap.Modal.getInstance(document.getElementById('editSpeakerScoresModal')).hide();
                    this.currentEditingSpeaker = null;

                    this.showToast('Speaker scores updated successfully!', 'success');
                } catch (error) {
                    console.error('Error saving speaker scores:', error);
                    this.showToast('Error saving speaker scores', 'error');
                }
            }

            viewTeam(teamId) {
                try {
                    const team = this.database.data.teams.find(t => t.id === teamId);
                    if (!team) return;

                    const school = this.database.getSchool(team.schoolId);

                    document.getElementById('viewTeamTitle').textContent = team.name;
                    document.getElementById('viewTeamSchool').textContent = school ? school.name : 'Unknown School';
                    document.getElementById('viewTeamClass').textContent = `${team.class} ${team.section ? `- ${team.section}` : ''}`;
                    document.getElementById('viewTeamScore').textContent = team.totalScore;

                    const membersContainer = document.getElementById('viewTeamMembers');
                    membersContainer.innerHTML = '';

                    const teamSpeakers = this.database.getTeamSpeakers(teamId);
                    
                    if (teamSpeakers.length === 0) {
                        membersContainer.innerHTML = '<div class="text-center p-3 text-muted">No speakers in this team</div>';
                    } else {
                        teamSpeakers.forEach(speaker => {
                            const memberDiv = document.createElement('div');
                            memberDiv.className = 'team-member-item border-bottom pb-2 mb-2';
                            memberDiv.innerHTML = `
                                <div class="d-flex justify-content-between align-items-center">
                                    <div>
                                        <strong>${speaker.name}</strong>
                                        <div class="round-scores">
                                            <div class="round-score">
                                                <div class="round-label">R1</div>
                                                <div class="round-value">${speaker.rounds[0] || 0}</div>
                                            </div>
                                            <div class="round-score">
                                                <div class="round-label">R2</div>
                                                <div class="round-value">${speaker.rounds[1] || 0}</div>
                                            </div>
                                            <div class="round-score">
                                                <div class="round-label">R3</div>
                                                <div class="round-value">${speaker.rounds[2] || 0}</div>
                                            </div>
                                            <div class="round-score bg-light">
                                                <div class="round-label">Total</div>
                                                <div class="round-value">${speaker.totalScore}</div>
                                            </div>
                                        </div>
                                    </div>
                                    <button class="btn btn-sm btn-outline-primary edit-speaker-scores-btn" data-speaker-id="${speaker.id}">
                                        <i class="fas fa-edit"></i>
                                    </button>
                                </div>
                            `;
                            membersContainer.appendChild(memberDiv);
                        });
                    }

                    this.currentViewingTeam = teamId;
                    const modal = new bootstrap.Modal(document.getElementById('viewTeamModal'));
                    modal.show();
                } catch (error) {
                    console.error('Error viewing team:', error);
                    this.showToast('Error viewing team', 'error');
                }
            }

            editTeam(teamId) {
                try {
                    const team = this.database.data.teams.find(t => t.id === teamId);
                    if (!team) return;

                    document.getElementById('editTeamId').value = team.id;
                    document.getElementById('editTeamName').value = team.name;
                    document.getElementById('editTeamSchool').value = team.schoolId;
                    document.getElementById('editTeamClass').value = team.class;
                    document.getElementById('editTeamStage').value = team.stage;
                    
                    // Handle section display
                    this.handleEditClassChange(team.class);
                    if (team.section) {
                        document.getElementById('editTeamSection').value = team.section;
                    }

                    // Populate speakers
                    const speakersContainer = document.getElementById('editSpeakersContainer');
                    speakersContainer.innerHTML = '';
                    
                    const teamSpeakers = this.database.getTeamSpeakers(teamId);
                    teamSpeakers.forEach(speaker => {
                        const speakerDiv = document.createElement('div');
                        speakerDiv.className = 'd-flex align-items-center mb-2';
                        speakerDiv.innerHTML = `
                            <input type="text" class="form-control me-2 edit-speaker-name" value="${speaker.name}" data-speaker-id="${speaker.id}">
                            <button type="button" class="btn btn-sm btn-outline-danger remove-speaker-btn" data-speaker-id="${speaker.id}">
                                <i class="fas fa-trash"></i>
                            </button>
                        `;
                        speakersContainer.appendChild(speakerDiv);
                    });

                    // Add empty field for new speaker
                    const newSpeakerDiv = document.createElement('div');
                    newSpeakerDiv.className = 'd-flex align-items-center mb-2';
                    newSpeakerDiv.innerHTML = `
                        <input type="text" class="form-control me-2 edit-speaker-name" placeholder="New speaker name">
                        <button type="button" class="btn btn-sm btn-outline-danger remove-speaker-btn" style="visibility: hidden;">
                            <i class="fas fa-trash"></i>
                        </button>
                    `;
                    speakersContainer.appendChild(newSpeakerDiv);

                    this.currentEditingTeam = teamId;
                    const modal = new bootstrap.Modal(document.getElementById('editTeamModal'));
                    modal.show();
                } catch (error) {
                    console.error('Error editing team:', error);
                    this.showToast('Error editing team', 'error');
                }
            }

            updateTeam() {
                try {
                    if (!this.currentEditingTeam) return;

                    const teamId = this.currentEditingTeam;
                    const teamName = document.getElementById('editTeamName').value.trim();
                    const teamSchool = parseInt(document.getElementById('editTeamSchool').value);
                    const teamClass = document.getElementById('editTeamClass').value;
                    const teamSection = document.getElementById('editTeamSection').value;
                    const teamStage = document.getElementById('editTeamStage').value;
                    
                    if (!teamName || !teamSchool || !teamClass) {
                        this.showToast('Please fill in all required fields', 'error');
                        return;
                    }

                    // Update team details
                    this.database.updateTeam(teamId, {
                        name: teamName,
                        schoolId: teamSchool,
                        class: teamClass,
                        section: teamSection,
                        stage: teamStage
                    });

                    // Update speakers
                    const speakerInputs = document.querySelectorAll('.edit-speaker-name');
                    speakerInputs.forEach(input => {
                        const speakerName = input.value.trim();
                        const speakerId = input.dataset.speakerId;
                        
                        if (speakerName) {
                            if (speakerId) {
                                // Update existing speaker
                                this.database.updateSpeaker(parseInt(speakerId), { name: speakerName });
                            } else {
                                // Add new speaker
                                this.database.addSpeaker({
                                    name: speakerName,
                                    teamId: teamId
                                });
                            }
                        }
                    });

                    this.updateUI();
                    bootstrap.Modal.getInstance(document.getElementById('editTeamModal')).hide();
                    this.currentEditingTeam = null;

                    this.showToast('Team updated successfully!', 'success');
                } catch (error) {
                    console.error('Error updating team:', error);
                    this.showToast('Error updating team', 'error');
                }
            }

            deleteTeam() {
                try {
                    if (!this.currentEditingTeam) return;

                    if (confirm('Are you sure you want to delete this team? This will also delete all speakers in this team.')) {
                        this.database.deleteTeam(this.currentEditingTeam);
                        
                        this.updateUI();
                        bootstrap.Modal.getInstance(document.getElementById('editTeamModal')).hide();
                        this.currentEditingTeam = null;

                        this.showToast('Team deleted successfully!', 'success');
                    }
                } catch (error) {
                    console.error('Error deleting team:', error);
                    this.showToast('Error deleting team', 'error');
                }
            }

            removeSpeakerFromEdit(speakerId) {
                try {
                    if (confirm('Are you sure you want to remove this speaker?')) {
                        this.database.deleteSpeaker(parseInt(speakerId));
                        
                        // Remove from UI
                        const speakerInput = document.querySelector(`.edit-speaker-name[data-speaker-id="${speakerId}"]`);
                        if (speakerInput) {
                            speakerInput.closest('.d-flex').remove();
                        }
                        
                        this.showToast('Speaker removed successfully!', 'success');
                    }
                } catch (error) {
                    console.error('Error removing speaker from edit:', error);
                    this.showToast('Error removing speaker', 'error');
                }
            }

            saveSchool() {
                try {
                    const schoolName = document.getElementById('schoolName').value.trim();
                    const schoolCode = document.getElementById('schoolCode').value.trim();

                    if (!schoolName) {
                        this.showToast('Please enter a school name', 'error');
                        return;
                    }

                    this.database.addSchool({
                        name: schoolName,
                        code: schoolCode || `SCH${(this.database.data.schools.length + 1).toString().padStart(3, '0')}`
                    });

                    this.updateUI();
                    bootstrap.Modal.getInstance(document.getElementById('addSchoolModal')).hide();
                    document.getElementById('schoolForm').reset();

                    this.showToast('School added successfully!', 'success');
                } catch (error) {
                    console.error('Error saving school:', error);
                    this.showToast('Error saving school', 'error');
                }
            }

            handleClassChange(selectedClass) {
                try {
                    const sectionContainer = document.getElementById('sectionContainer');
                    const sectionSelect = document.getElementById('teamSection');
                    
                    if (['S1', 'S2', 'S3'].includes(selectedClass)) {
                        sectionContainer.style.display = 'block';
                        sectionSelect.innerHTML = `
                            <option value="">Select Section</option>
                            <option value="A">A</option>
                            <option value="B">B</option>
                            <option value="C">C</option>
                            <option value="D">D</option>
                            <option value="E">E</option>
                            <option value="F">F</option>
                        `;
                    } else if (['S4', 'S5', 'S6'].includes(selectedClass)) {
                        sectionContainer.style.display = 'block';
                        sectionSelect.innerHTML = `
                            <option value="">Select Stream</option>
                            <option value="Stream 1">Stream 1</option>
                            <option value="Stream 2">Stream 2</option>
                            <option value="MCE">MCE</option>
                            <option value="LFK">LFK</option>
                            <option value="Languages">Languages</option>
                            <option value="PCM">PCM</option>
                            <option value="MCB">MCB</option>
                            <option value="MEG">MEG</option>
                            <option value="PCB">PCB</option>
                        `;
                    } else {
                        sectionContainer.style.display = 'none';
                    }
                } catch (error) {
                    console.error('Error handling class change:', error);
                }
            }

            handleEditClassChange(selectedClass) {
                try {
                    const sectionContainer = document.getElementById('editSectionContainer');
                    const sectionSelect = document.getElementById('editTeamSection');
                    
                    if (['S1', 'S2', 'S3'].includes(selectedClass)) {
                        sectionContainer.style.display = 'block';
                        sectionSelect.innerHTML = `
                            <option value="">Select Section</option>
                            <option value="A">A</option>
                            <option value="B">B</option>
                            <option value="C">C</option>
                            <option value="D">D</option>
                            <option value="E">E</option>
                            <option value="F">F</option>
                        `;
                    } else if (['S4', 'S5', 'S6'].includes(selectedClass)) {
                        sectionContainer.style.display = 'block';
                        sectionSelect.innerHTML = `
                            <option value="">Select Stream</option>
                            <option value="Stream 1">Stream 1</option>
                            <option value="Stream 2">Stream 2</option>
                            <option value="MCE">MCE</option>
                            <option value="LFK">LFK</option>
                            <option value="Languages">Languages</option>
                            <option value="PCM">PCM</option>
                            <option value="MCB">MCB</option>
                            <option value="MEG">MEG</option>
                            <option value="PCB">PCB</option>
                        `;
                    } else {
                        sectionContainer.style.display = 'none';
                    }
                } catch (error) {
                    console.error('Error handling edit class change:', error);
                }
            }

            addSpeakerField() {
                try {
                    const container = document.getElementById('speakersContainer');
                    const input = document.createElement('input');
                    input.type = 'text';
                    input.className = 'form-control mb-2 speaker-name';
                    input.placeholder = 'Speaker ' + (container.children.length + 1);
                    container.appendChild(input);
                } catch (error) {
                    console.error('Error adding speaker field:', error);
                }
            }

            addEditSpeakerField() {
                try {
                    const container = document.getElementById('editSpeakersContainer');
                    const newSpeakerDiv = document.createElement('div');
                    newSpeakerDiv.className = 'd-flex align-items-center mb-2';
                    newSpeakerDiv.innerHTML = `
                        <input type="text" class="form-control me-2 edit-speaker-name" placeholder="New speaker name">
                        <button type="button" class="btn btn-sm btn-outline-danger remove-speaker-btn" style="visibility: hidden;">
                            <i class="fas fa-trash"></i>
                        </button>
                    `;
                    container.appendChild(newSpeakerDiv);
                } catch (error) {
                    console.error('Error adding edit speaker field:', error);
                }
            }

            saveTeam() {
                try {
                    const teamName = document.getElementById('teamName').value.trim();
                    const teamSchool = parseInt(document.getElementById('teamSchool').value);
                    const teamClass = document.getElementById('teamClass').value;
                    const teamSection = document.getElementById('teamSection').value;
                    const speakerInputs = document.querySelectorAll('.speaker-name');
                    
                    if (!teamName || !teamSchool || !teamClass) {
                        this.showToast('Please fill in all required fields', 'error');
                        return;
                    }

                    if (['S1', 'S2', 'S3', 'S4', 'S5', 'S6'].includes(teamClass) && !teamSection) {
                        this.showToast('Please select a section/stream', 'error');
                        return;
                    }

                    const teamData = {
                        name: teamName,
                        schoolId: teamSchool,
                        class: teamClass,
                        section: teamSection
                    };

                    const newTeam = this.database.addTeam(teamData);

                    // Create speakers
                    for (const input of speakerInputs) {
                        if (input.value.trim()) {
                            this.database.addSpeaker({
                                name: input.value.trim(),
                                teamId: newTeam.id
                            });
                        }
                    }

                    this.updateUI();
                    bootstrap.Modal.getInstance(document.getElementById('addTeamModal')).hide();
                    document.getElementById('teamForm').reset();
                    document.getElementById('sectionContainer').style.display = 'none';

                    // Reset speaker fields
                    const container = document.getElementById('speakersContainer');
                    container.innerHTML = '';
                    for (let i = 0; i < 3; i++) {
                        const input = document.createElement('input');
                        input.type = 'text';
                        input.className = 'form-control mb-2 speaker-name';
                        input.placeholder = 'Speaker ' + (i + 1);
                        input.required = i < 2;
                        container.appendChild(input);
                    }

                    this.showToast('Team added successfully!', 'success');
                } catch (error) {
                    console.error('Error saving team:', error);
                    this.showToast('Error saving team', 'error');
                }
            }

            saveSpeaker() {
                try {
                    const speakerName = document.getElementById('speakerName').value.trim();
                    const teamId = parseInt(document.getElementById('speakerTeam').value);

                    if (!speakerName || !teamId) {
                        this.showToast('Please fill in all required fields', 'error');
                        return;
                    }

                    // Populate team select
                    const teamSelect = document.getElementById('speakerTeam');
                    teamSelect.innerHTML = '<option value="" disabled selected>Select Team</option>';
                    this.database.data.teams.forEach(team => {
                        const option = document.createElement('option');
                        option.value = team.id;
                        option.textContent = team.name;
                        teamSelect.appendChild(option);
                    });

                    this.database.addSpeaker({
                        name: speakerName,
                        teamId: teamId
                    });

                    this.updateUI();
                    bootstrap.Modal.getInstance(document.getElementById('addSpeakerModal')).hide();
                    document.getElementById('speakerForm').reset();

                    this.showToast('Speaker added successfully!', 'success');
                } catch (error) {
                    console.error('Error saving speaker:', error);
                    this.showToast('Error saving speaker', 'error');
                }
            }

            // Excel file handling
            handleFileSelect(event) {
                try {
                    const file = event.target.files[0];
                    if (!file) return;

                    const reader = new FileReader();
                    reader.onload = (e) => {
                        try {
                            const data = new Uint8Array(e.target.result);
                            const workbook = XLSX.read(data, { type: 'array' });
                            
                            // Get first worksheet
                            const worksheet = workbook.Sheets[workbook.SheetNames[0]];
                            const jsonData = XLSX.utils.sheet_to_json(worksheet);
                            
                            this.previewExcelData(jsonData);
                        } catch (error) {
                            console.error('Error reading Excel file:', error);
                            this.showToast('Error reading Excel file', 'error');
                        }
                    };
                    reader.readAsArrayBuffer(file);
                } catch (error) {
                    console.error('Error handling file select:', error);
                    this.showToast('Error handling file', 'error');
                }
            }

            parsePastedData() {
                try {
                    const pastedText = document.getElementById('pasteData').value.trim();
                    if (!pastedText) {
                        this.showToast('Please paste some data first', 'error');
                        return;
                    }

                    const lines = pastedText.split('\n').filter(line => line.trim());
                    const jsonData = [];
                    
                    lines.forEach(line => {
                        const parts = line.split('|').map(part => part.trim());
                        if (parts.length >= 2) {
                            jsonData.push({
                                Members: parts[0],
                                Classes: parts[1],
                                School: parts[2] || 'Default School'
                            });
                        }
                    });
                    
                    this.previewExcelData(jsonData);
                } catch (error) {
                    console.error('Error parsing pasted data:', error);
                    this.showToast('Error parsing pasted data', 'error');
                }
            }

            previewExcelData(data) {
                try {
                    if (!data || data.length === 0) {
                        this.showToast('No valid data found in the file', 'error');
                        return;
                    }

                    // Show preview
                    document.getElementById('previewData').style.display = 'block';
                    
                    // Populate preview table
                    const tbody = document.querySelector('#previewTable tbody');
                    tbody.innerHTML = '';
                    
                    data.slice(0, 10).forEach(row => { // Show first 10 rows
                        const tr = document.createElement('tr');
                        tr.innerHTML = `
                            <td>${row.Members || ''}</td>
                            <td>${row.Classes || ''}</td>
                            <td>${row.School || 'Default School'}</td>
                        `;
                        tbody.appendChild(tr);
                    });
                    
                    if (data.length > 10) {
                        const tr = document.createElement('tr');
                        tr.innerHTML = `<td colspan="3" class="text-center text-muted">... and ${data.length - 10} more rows</td>`;
                        tbody.appendChild(tr);
                    }
                    
                    // Parse teams for stats
                    const teamsMap = {};
                    const schoolsMap = {};
                    
                    data.forEach(row => {
                        if (row.Members && row.Classes) {
                            const memberName = row.Members.trim();
                            const classTeam = row.Classes.trim();
                            const schoolName = row.School ? row.School.trim() : 'Default School';
                            
                            if (!schoolsMap[schoolName]) {
                                schoolsMap[schoolName] = true;
                            }
                            
                            // Parse class and team name
                            const teamMatch = classTeam.match(/(S[1-6])([A-Z]?)\s*(TEAM\s*\d+|.*)/i);
                            if (teamMatch) {
                                const classLevel = teamMatch[1];
                                const section = teamMatch[2] || '';
                                const teamName = teamMatch[3].trim();
                                
                                const fullTeamName = `${schoolName} - ${classLevel}${section} ${teamName}`;
                                
                                if (!teamsMap[fullTeamName]) {
                                    teamsMap[fullTeamName] = {
                                        name: fullTeamName,
                                        class: classLevel,
                                        section: section,
                                        members: []
                                    };
                                }
                                
                                teamsMap[fullTeamName].members.push(memberName);
                            }
                        }
                    });
                    
                    const teamCount = Object.keys(teamsMap).length;
                    const memberCount = Object.values(teamsMap).reduce((sum, team) => sum + team.members.length, 0);
                    const schoolCount = Object.keys(schoolsMap).length;
                    
                    document.getElementById('previewStats').textContent = 
                        `Found ${teamCount} teams with ${memberCount} members from ${schoolCount} schools`;
                    
                    // Enable import button
                    document.getElementById('importDataBtn').disabled = false;
                    
                    // Store data for import
                    this.pendingImportData = data;
                } catch (error) {
                    console.error('Error previewing Excel data:', error);
                    this.showToast('Error previewing data', 'error');
                }
            }

            importData() {
                try {
                    if (!this.pendingImportData) return;
                    
                    const importedTeams = this.database.importTeamsFromExcelData(this.pendingImportData);
                    
                    this.updateUI();
                    bootstrap.Modal.getInstance(document.getElementById('uploadExcelModal')).hide();
                    
                    // Reset form
                    document.getElementById('previewData').style.display = 'none';
                    document.getElementById('importDataBtn').disabled = true;
                    document.getElementById('pasteData').value = '';
                    document.getElementById('excelFileInput').value = '';
                    
                    this.showToast(`Successfully imported ${importedTeams.length} teams!`, 'success');
                } catch (error) {
                    console.error('Error importing data:', error);
                    this.showToast('Error importing data', 'error');
                }
            }

            updateResults() {
                try {
                    const stats = this.database.calculateTeamStats();
                    
                    document.getElementById('total-participants').textContent = stats.totalParticipants;
                    document.getElementById('total-teams').textContent = stats.totalTeams;
                    document.getElementById('total-schools').textContent = stats.totalSchools;
                    document.getElementById('average-score').textContent = stats.averageScore;

                    // Update top speakers list
                    const topSpeakersList = document.getElementById('top-speakers-list');
                    if (topSpeakersList) {
                        topSpeakersList.innerHTML = '';

                        const speakerRankings = this.database.getSpeakerRankings().slice(0, 10);
                        
                        if (speakerRankings.length === 0) {
                            topSpeakersList.innerHTML = '<li class="text-center p-4 text-muted">No speakers yet</li>';
                            return;
                        }
                        
                        speakerRankings.forEach((speaker, index) => {
                            const trendIcon = speaker.trend === 'up' ? 'fa-arrow-up trend-up' : 
                                            speaker.trend === 'down' ? 'fa-arrow-down trend-down' : 'fa-minus trend-stable';
                            const medal = speaker.medal ? `<i class="fas fa-medal" style="color: ${speaker.medal};"></i>` : '';
                            
                            const li = document.createElement('li');
                            li.innerHTML = `
                                <div class="speaker-rank">${speaker.rank}</div>
                                <div class="speaker-info">
                                    <div class="speaker-name">${speaker.name}</div>
                                    <div class="speaker-team">${speaker.teamName} - ${speaker.schoolName}</div>
                                </div>
                                <div class="speaker-score">
                                    ${speaker.totalScore} pts
                                    <i class="fas ${trendIcon} ms-1"></i>
                                </div>
                                <div class="speaker-medal">${medal}</div>
                            `;
                            topSpeakersList.appendChild(li);
                        });
                    }
                } catch (error) {
                    console.error('Error updating results:', error);
                }
            }

            updateSpeakersList() {
                try {
                    const allSpeakersList = document.getElementById('all-speakers-list');
                    if (!allSpeakersList) return;

                    allSpeakersList.innerHTML = '';

                    const speakerRankings = this.database.getSpeakerRankings();
                    
                    if (speakerRankings.length === 0) {
                        allSpeakersList.innerHTML = '<li class="text-center p-4 text-muted">No speakers yet</li>';
                        return;
                    }
                    
                    speakerRankings.forEach((speaker, index) => {
                        const trendIcon = speaker.trend === 'up' ? 'fa-arrow-up trend-up' : 
                                        speaker.trend === 'down' ? 'fa-arrow-down trend-down' : 'fa-minus trend-stable';
                        const medal = speaker.medal ? `<i class="fas fa-medal" style="color: ${speaker.medal};"></i>` : '';
                        
                        const li = document.createElement('li');
                        li.innerHTML = `
                            <div class="speaker-rank">${speaker.rank}</div>
                            <div class="speaker-info">
                                <div class="speaker-name">${speaker.name}</div>
                                <div class="speaker-team">${speaker.teamName} - ${speaker.schoolName}</div>
                                <div class="round-scores mt-1">
                                    <small class="text-muted">
                                        R1: ${speaker.rounds[0] || 0} | 
                                        R2: ${speaker.rounds[1] || 0} | 
                                        R3: ${speaker.rounds[2] || 0}
                                    </small>
                                </div>
                            </div>
                            <div class="speaker-score">
                                ${speaker.totalScore} pts
                                <i class="fas ${trendIcon} ms-1"></i>
                            </div>
                            <div class="speaker-medal">${medal}</div>
                            <button class="btn btn-sm btn-outline-primary edit-speaker-scores-btn ms-2" data-speaker-id="${speaker.id}">
                                <i class="fas fa-edit"></i>
                            </button>
                        `;
                        allSpeakersList.appendChild(li);
                    });

                    // Populate team select for adding speakers
                    const teamSelect = document.getElementById('speakerTeam');
                    if (teamSelect) {
                        teamSelect.innerHTML = '<option value="" disabled selected>Select Team</option>';
                        this.database.data.teams.forEach(team => {
                            const option = document.createElement('option');
                            option.value = team.id;
                            option.textContent = team.name;
                            teamSelect.appendChild(option);
                        });
                    }
                } catch (error) {
                    console.error('Error updating speakers list:', error);
                }
            }

            filterSpeakers(query) {
                try {
                    const filteredSpeakers = this.database.data.speakers.filter(speaker => 
                        speaker.name.toLowerCase().includes(query.toLowerCase())
                    );
                    
                    const allSpeakersList = document.getElementById('all-speakers-list');
                    allSpeakersList.innerHTML = '';

                    filteredSpeakers.forEach((speaker, index) => {
                        const team = this.database.getSpeakerTeam(speaker.id);
                        const school = team ? this.database.getSchool(team.schoolId) : null;
                        const li = document.createElement('li');
                        li.innerHTML = `
                            <div class="speaker-rank">${index + 1}</div>
                            <div class="speaker-info">
                                <div class="speaker-name">${speaker.name}</div>
                                <div class="speaker-team">${team ? team.name : 'No Team'} - ${school ? school.name : 'Unknown School'}</div>
                            </div>
                            <div class="speaker-score">${speaker.totalScore} pts</div>
                            <button class="btn btn-sm btn-outline-primary edit-speaker-scores-btn ms-2" data-speaker-id="${speaker.id}">
                                <i class="fas fa-edit"></i>
                            </button>
                        `;
                        allSpeakersList.appendChild(li);
                    });
                } catch (error) {
                    console.error('Error filtering speakers:', error);
                }
            }

            updateTeamsList() {
                try {
                    const teamsContainer = document.getElementById('teams-container');
                    if (!teamsContainer) return;

                    teamsContainer.innerHTML = '';

                    const teamRankings = this.database.getTeamRankings();
                    
                    if (teamRankings.length === 0) {
                        teamsContainer.innerHTML = '<div class="text-center p-4 text-muted">No teams yet</div>';
                        return;
                    }
                    
                    teamRankings.forEach(team => {
                        const teamSpeakers = this.database.getTeamSpeakers(team.id);
                        const school = this.database.getSchool(team.schoolId);
                        const trendIcon = team.trend === 'up' ? 'fa-arrow-up trend-up' : 
                                        team.trend === 'down' ? 'fa-arrow-down trend-down' : 'fa-minus trend-stable';
                        
                        const card = document.createElement('div');
                        card.className = 'result-card';
                        card.innerHTML = `
                            <h5>${team.name} 
                                ${team.badges.map(badge => `<span class="team-badge badge-${badge}">${badge}</span>`).join('')}
                            </h5>
                            <div class="score">
                                ${team.totalScore}
                                <i class="fas ${trendIcon} ms-1"></i>
                            </div>
                            <div class="score-label">Total Points</div>
                            <div class="trend">
                                <i class="fas fa-school me-1"></i> ${school ? school.name : 'Unknown School'}
                            </div>
                            <div class="trend">
                                <i class="fas fa-users me-1"></i> ${teamSpeakers.length} speakers
                            </div>
                            <div class="mt-3">
                                <strong>Class:</strong> ${team.class} ${team.section ? `- ${team.section}` : ''}
                            </div>
                            <div class="mt-2">
                                <strong>Stage:</strong> 
                                <span class="badge ${team.stage === 'octa' ? 'bg-primary' : team.stage === 'quarter' ? 'bg-info' : team.stage === 'semi' ? 'bg-warning' : team.stage === 'final' ? 'bg-success' : 'bg-secondary'}">
                                    ${team.stage}
                                </span>
                            </div>
                            <div class="level-controls">
                                <button class="btn btn-sm btn-success level-up-btn" data-team-id="${team.id}">
                                    <i class="fas fa-arrow-up"></i>
                                </button>
                                <button class="btn btn-sm btn-warning level-down-btn" data-team-id="${team.id}">
                                    <i class="fas fa-arrow-down"></i>
                                </button>
                            </div>
                            <div class="mt-3">
                                <button class="btn btn-sm btn-outline-primary view-team-btn me-2" data-team-id="${team.id}">
                                    <i class="fas fa-eye me-1"></i> View Details
                                </button>
                                <button class="btn btn-sm btn-outline-warning edit-team-btn" data-team-id="${team.id}">
                                    <i class="fas fa-edit me-1"></i> Edit Team
                                </button>
                            </div>
                        `;
                        teamsContainer.appendChild(card);
                    });
                } catch (error) {
                    console.error('Error updating teams list:', error);
                }
            }

            updateSchoolsList() {
                try {
                    const schoolsContainer = document.getElementById('schools-container');
                    if (!schoolsContainer) return;

                    schoolsContainer.innerHTML = '';

                    const schoolRankings = this.database.getSchoolRankings();
                    
                    if (schoolRankings.length === 0) {
                        schoolsContainer.innerHTML = '<div class="text-center p-4 text-muted">No schools yet</div>';
                        return;
                    }
                    
                    schoolRankings.forEach(school => {
                        const schoolTeams = this.database.data.teams.filter(t => t.schoolId === school.id);
                        const trendIcon = school.trend === 'up' ? 'fa-arrow-up trend-up' : 
                                        school.trend === 'down' ? 'fa-arrow-down trend-down' : 'fa-minus trend-stable';
                        
                        const card = document.createElement('div');
                        card.className = 'result-card';
                        card.innerHTML = `
                            <h5>${school.name}</h5>
                            <div class="score">
                                ${school.totalPoints}
                                <i class="fas ${trendIcon} ms-1"></i>
                            </div>
                            <div class="score-label">Total Points</div>
                            <div class="trend">
                                <i class="fas fa-trophy me-1"></i> Rank #${school.rank}
                            </div>
                            <div class="trend">
                                <i class="fas fa-users me-1"></i> ${schoolTeams.length} teams
                            </div>
                            <div class="mt-3">
                                <strong>School Code:</strong> ${school.code}
                            </div>
                            <div class="mt-2">
                                <strong>Active Teams:</strong> 
                                <span class="badge bg-success">${schoolTeams.filter(t => t.status === 'active').length}</span>
                            </div>
                        `;
                        schoolsContainer.appendChild(card);
                    });
                } catch (error) {
                    console.error('Error updating schools list:', error);
                }
            }

            filterTeams(query) {
                try {
                    const filteredTeams = this.database.data.teams.filter(team => 
                        team.name.toLowerCase().includes(query.toLowerCase()) ||
                        (this.database.getSchool(team.schoolId)?.name.toLowerCase().includes(query.toLowerCase()))
                    );
                    
                    const teamsContainer = document.getElementById('teams-container');
                    teamsContainer.innerHTML = '';

                    filteredTeams.forEach(team => {
                        const teamSpeakers = this.database.getTeamSpeakers(team.id);
                        const school = this.database.getSchool(team.schoolId);
                        
                        const card = document.createElement('div');
                        card.className = 'result-card';
                        card.innerHTML = `
                            <h5>${team.name}</h5>
                            <div class="score">${team.totalScore}</div>
                            <div class="score-label">Total Points</div>
                            <div class="trend">
                                <i class="fas fa-school me-1"></i> ${school ? school.name : 'Unknown School'}
                            </div>
                            <div class="trend">
                                <i class="fas fa-users me-1"></i> ${teamSpeakers.length} speakers
                            </div>
                            <div class="mt-3">
                                <strong>Class:</strong> ${team.class} ${team.section ? `- ${team.section}` : ''}
                            </div>
                            <div class="mt-3">
                                <button class="btn btn-sm btn-outline-primary view-team-btn me-2" data-team-id="${team.id}">
                                    <i class="fas fa-eye me-1"></i> View Details
                                </button>
                                <button class="btn btn-sm btn-outline-warning edit-team-btn" data-team-id="${team.id}">
                                    <i class="fas fa-edit me-1"></i> Edit Team
                                </button>
                            </div>
                        `;
                        teamsContainer.appendChild(card);
                    });
                } catch (error) {
                    console.error('Error filtering teams:', error);
                }
            }

            updateLeaderboard() {
                try {
                    const leaderboardContainer = document.getElementById('leaderboard-teams');
                    if (!leaderboardContainer) return;

                    leaderboardContainer.innerHTML = '';

                    const teamRankings = this.database.getTeamRankings();

                    if (teamRankings.length === 0) {
                        leaderboardContainer.innerHTML = '<div class="text-center p-4 text-muted">No teams in the leaderboard</div>';
                        return;
                    }

                    teamRankings.forEach((team, index) => {
                        const teamSpeakers = this.database.getTeamSpeakers(team.id);
                        const position = team.rank;
                        const trendIcon = team.trend === 'up' ? 'fa-arrow-up trend-up' : 
                                        team.trend === 'down' ? 'fa-arrow-down trend-down' : 'fa-minus trend-stable';
                        
                        const card = document.createElement('div');
                        card.className = 'result-card';
                        card.innerHTML = `
                            <div class="d-flex justify-content-between align-items-start">
                                <div>
                                    <h5>
                                        <span class="badge bg-primary me-2">#${position}</span>
                                        ${team.name}
                                        ${team.badges.map(badge => `<span class="team-badge badge-${badge}">${badge}</span>`).join('')}
                                    </h5>
                                    <div class="score">
                                        ${team.totalScore} points
                                        <i class="fas ${trendIcon} ms-1"></i>
                                    </div>
                                    <div class="score-label">Total Score</div>
                                    <div class="school-badge mt-1">${team.schoolName}</div>
                                </div>
                                <div class="text-end">
                                    <div class="trend">
                                        <i class="fas fa-users me-1"></i> ${teamSpeakers.length} speakers
                                    </div>
                                    <div class="mt-1">
                                        <strong>${team.class}</strong> ${team.section ? `- ${team.section}` : ''}
                                    </div>
                                    <div class="mt-1">
                                        <span class="badge ${team.stage === 'octa' ? 'bg-primary' : team.stage === 'quarter' ? 'bg-info' : team.stage === 'semi' ? 'bg-warning' : team.stage === 'final' ? 'bg-success' : 'bg-secondary'}">
                                            ${team.stage}
                                        </span>
                                    </div>
                                </div>
                            </div>
                            <div class="mt-3">
                                <button class="btn btn-sm btn-outline-primary view-team-btn w-100" data-team-id="${team.id}">
                                    <i class="fas fa-chart-bar me-1"></i> View Performance
                                </button>
                            </div>
                        `;
                        leaderboardContainer.appendChild(card);
                    });
                } catch (error) {
                    console.error('Error updating leaderboard:', error);
                }
            }

            updateAnalytics() {
                try {
                    const analyticsPage = document.getElementById('analytics-page');
                    if (!analyticsPage) return;

                    const stats = this.database.calculateTeamStats();
                    const issues = this.database.getAnalytics();
                    const schoolRankings = this.database.getSchoolRankings().slice(0, 5);
                    const teamRankings = this.database.getTeamRankings().slice(0, 5);
                    const speakerRankings = this.database.getSpeakerRankings().slice(0, 5);
                    
                    analyticsPage.innerHTML = `
                        <div class="analytics-grid">
                            <div class="analytics-card">
                                <h5><i class="fas fa-chart-bar me-2"></i>Tournament Statistics</h5>
                                <div class="row text-center">
                                    <div class="col-md-3">
                                        <h3 class="text-primary">${stats.totalTeams}</h3>
                                        <p class="text-muted">Total Teams</p>
                                    </div>
                                    <div class="col-md-3">
                                        <h3 class="text-success">${stats.totalParticipants}</h3>
                                        <p class="text-muted">Total Speakers</p>
                                    </div>
                                    <div class="col-md-3">
                                        <h3 class="text-warning">${this.database.data.tournament.champion ? 1 : 0}</h3>
                                        <p class="text-muted">Champion</p>
                                    </div>
                                    <div class="col-md-3">
                                        <h3 class="text-info">${stats.totalSchools}</h3>
                                        <p class="text-muted">Schools</p>
                                    </div>
                                </div>
                            </div>

                            <div class="analytics-card">
                                <h5><i class="fas fa-exclamation-triangle me-2"></i>Critical Issues</h5>
                                <div class="issue-list">
                                    ${issues.filter(issue => issue.severity === 'high')
                                        .map(issue => this.createIssueItem(issue)).join('')}
                                    ${issues.filter(issue => issue.severity === 'high').length === 0 ? 
                                        '<div class="text-center p-3 text-muted">No critical issues</div>' : ''}
                                </div>
                            </div>
                        </div>
                        
                        <div class="analytics-grid">
                            <div class="analytics-card">
                                <h5><i class="fas fa-trophy me-2"></i>Top Schools</h5>
                                <div class="issue-list">
                                    ${schoolRankings.map(school => `
                                        <div class="issue-item">
                                            <div class="d-flex justify-content-between align-items-center">
                                                <div>
                                                    <span class="badge bg-primary me-2">#${school.rank}</span>
                                                    <strong>${school.name}</strong>
                                                </div>
                                                <div>
                                                    <span class="text-success">${school.totalPoints} pts</span>
                                                    <i class="fas ${school.trend === 'up' ? 'fa-arrow-up trend-up' : school.trend === 'down' ? 'fa-arrow-down trend-down' : 'fa-minus trend-stable'} ms-1"></i>
                                                </div>
                                            </div>
                                        </div>
                                    `).join('')}
                                    ${schoolRankings.length === 0 ? 
                                        '<div class="text-center p-3 text-muted">No school data</div>' : ''}
                                </div>
                            </div>
                            
                            <div class="analytics-card">
                                <h5><i class="fas fa-users me-2"></i>Top Teams</h5>
                                <div class="issue-list">
                                    ${teamRankings.map(team => `
                                        <div class="issue-item">
                                            <div class="d-flex justify-content-between align-items-center">
                                                <div>
                                                    <span class="badge bg-info me-2">#${team.rank}</span>
                                                    <strong>${team.name}</strong>
                                                    <br><small class="text-muted">${team.schoolName}</small>
                                                </div>
                                                <div>
                                                    <span class="text-warning">${team.totalScore} pts</span>
                                                    <i class="fas ${team.trend === 'up' ? 'fa-arrow-up trend-up' : team.trend === 'down' ? 'fa-arrow-down trend-down' : 'fa-minus trend-stable'} ms-1"></i>
                                                </div>
                                            </div>
                                        </div>
                                    `).join('')}
                                    ${teamRankings.length === 0 ? 
                                        '<div class="text-center p-3 text-muted">No team data</div>' : ''}
                                </div>
                            </div>
                        </div>

                        <div class="analytics-grid">
                            <div class="analytics-card">
                                <h5><i class="fas fa-user me-2"></i>Top Speakers</h5>
                                <div class="issue-list">
                                    ${speakerRankings.map(speaker => `
                                        <div class="issue-item">
                                            <div class="d-flex justify-content-between align-items-center">
                                                <div>
                                                    <span class="badge ${speaker.medal ? 'bg-warning' : 'bg-secondary'} me-2">#${speaker.rank}</span>
                                                    <strong>${speaker.name}</strong>
                                                    <br><small class="text-muted">${speaker.teamName} - ${speaker.schoolName}</small>
                                                </div>
                                                <div>
                                                    <span class="text-primary">${speaker.totalScore} pts</span>
                                                    ${speaker.medal ? `<i class="fas fa-medal ms-1" style="color: ${speaker.medal};"></i>` : ''}
                                                    <i class="fas ${speaker.trend === 'up' ? 'fa-arrow-up trend-up' : speaker.trend === 'down' ? 'fa-arrow-down trend-down' : 'fa-minus trend-stable'} ms-1"></i>
                                                </div>
                                            </div>
                                        </div>
                                    `).join('')}
                                    ${speakerRankings.length === 0 ? 
                                        '<div class="text-center p-3 text-muted">No speaker data</div>' : ''}
                                </div>
                            </div>

                            <div class="analytics-card">
                                <h5><i class="fas fa-lightbulb me-2"></i>Suggestions</h5>
                                <div class="issue-list">
                                    ${issues.filter(issue => issue.severity === 'low')
                                        .map(issue => this.createIssueItem(issue)).join('')}
                                    ${this.database.data.teams.length < 8 ? `
                                        <div class="issue-item" data-issue='${JSON.stringify({type: 'add_more_teams', action: 'add_teams'})}'>
                                            <div class="d-flex justify-content-between align-items-center">
                                                <div>
                                                    <span class="issue-severity severity-low">SUGGESTION</span>
                                                    <span>Add more teams for better tournament experience</span>
                                                </div>
                                                <i class="fas fa-chevron-right text-muted"></i>
                                            </div>
                                        </div>
                                    ` : ''}
                                    ${issues.filter(issue => issue.severity === 'low').length === 0 && this.database.data.teams.length >= 8 ? 
                                        '<div class="text-center p-3 text-muted">No suggestions</div>' : ''}
                                </div>
                            </div>
                        </div>
                    `;
                } catch (error) {
                    console.error('Error updating analytics:', error);
                }
            }

            createIssueItem(issue) {
                try {
                    return `
                        <div class="issue-item" data-issue='${JSON.stringify(issue)}'>
                            <div class="d-flex justify-content-between align-items-center">
                                <div>
                                    <span class="issue-severity severity-${issue.severity}">
                                        ${issue.severity.toUpperCase()}
                                    </span>
                                    <span>${issue.message}</span>
                                </div>
                                <i class="fas fa-chevron-right text-muted"></i>
                            </div>
                        </div>
                    `;
                } catch (error) {
                    console.error('Error creating issue item:', error);
                    return '';
                }
            }

            handleAnalyticsIssue(issue) {
                try {
                    switch (issue.action) {
                        case 'add_speakers':
                            this.showPage('teams');
                            this.showToast(`Please add speakers to team`, 'info');
                            break;
                        case 'update_scores':
                            this.showPage('speakers');
                            this.editSpeakerScores(issue.speakerId);
                            break;
                        case 'add_teams':
                            this.showPage('teams');
                            this.showToast('Add more teams for better tournament experience', 'info');
                            break;
                        default:
                            this.showToast(`Handling issue: ${issue.message}`, 'info');
                    }
                } catch (error) {
                    console.error('Error handling analytics issue:', error);
                }
            }

            updateSettings() {
                try {
                    const settingsPage = document.getElementById('settings-page');
                    if (!settingsPage) return;

                    settingsPage.innerHTML = `
                        <div class="settings-section">
                            <h4><i class="fas fa-database me-2"></i>Data Management</h4>
                            <div class="mb-3">
                                <button class="btn btn-outline-danger w-100" id="clearDataBtn">
                                    <i class="fas fa-trash me-2"></i>Clear All Data
                                </button>
                                <small class="text-muted">This will delete all teams, speakers, and tournament progress</small>
                            </div>
                        </div>

                        <div class="settings-section">
                            <h4><i class="fas fa-info-circle me-2"></i>Tournament Information</h4>
                            <div class="row">
                                <div class="col-md-6">
                                    <strong>Current Stage:</strong> ${this.formatStageName(this.database.data.tournament.currentStage)}
                                </div>
                                <div class="col-md-6">
                                    <strong>Total Teams:</strong> ${this.database.data.teams.length}
                                </div>
                                <div class="col-md-6 mt-2">
                                    <strong>Total Speakers:</strong> ${this.database.data.speakers.length}
                                </div>
                                <div class="col-md-6 mt-2">
                                    <strong>Total Schools:</strong> ${this.database.data.schools.filter(s => s.teams.length > 0).length}
                                </div>
                                <div class="col-md-6 mt-2">
                                    <strong>Champion:</strong> ${this.database.data.tournament.champion ? 
                                        this.database.data.teams.find(t => t.id === this.database.data.tournament.champion)?.name : 'Not declared'}
                                </div>
                            </div>
                        </div>
                    `;

                    document.getElementById('clearDataBtn').addEventListener('click', () => {
                        if (confirm(' ARE YOU ABSOLUTELY SURE?\n\nThis will delete ALL teams, speakers, and tournament progress. This action cannot be undone!')) {
                            this.database.clearData();
                            this.updateUI();
                            this.showToast('All data cleared successfully!', 'success');
                        }
                    });
                } catch (error) {
                    console.error('Error updating settings:', error);
                }
            }

            refreshAnalytics() {
                try {
                    this.updateAnalytics();
                    this.showToast('Analytics refreshed!', 'success');
                } catch (error) {
                    console.error('Error refreshing analytics:', error);
                    this.showToast('Error refreshing analytics', 'error');
                }
            }

            // Utility methods
            capitalize(str) {
                try {
                    return str.charAt(0).toUpperCase() + str.slice(1);
                } catch (error) {
                    console.error('Error capitalizing string:', error);
                    return str;
                }
            }

            formatStageName(stage) {
                try {
                    const names = {
                        octa: 'Octa-Finals',
                        quarter: 'Quarter-Finals',
                        semi: 'Semi-Finals',
                        final: 'Finals'
                    };
                    return names[stage] || stage;
                } catch (error) {
                    console.error('Error formatting stage name:', error);
                    return stage;
                }
            }

            showToast(message, type = 'info') {
                try {
                    const toastContainer = document.querySelector('.toast-container');
                    const toastId = 'toast-' + Date.now();
                    
                    const bgClass = {
                        success: 'bg-success',
                        error: 'bg-danger',
                        warning: 'bg-warning',
                        info: 'bg-info'
                    }[type] || 'bg-info';

                    const toastHTML = `
                        <div id="${toastId}" class="toast align-items-center text-white ${bgClass} border-0" role="alert">
                            <div class="d-flex">
                                <div class="toast-body">${message}</div>
                                <button type="button" class="btn-close btn-close-white me-2 m-auto" data-bs-dismiss="toast"></button>
                            </div>
                        </div>
                    `;

                    toastContainer.insertAdjacentHTML('beforeend', toastHTML);
                    
                    const toastElement = document.getElementById(toastId);
                    const toast = new bootstrap.Toast(toastElement, { delay: 5000 });
                    toast.show();

                    toastElement.addEventListener('hidden.bs.toast', () => {
                        toastElement.remove();
                    });
                } catch (error) {
                    console.error('Error showing toast:', error);
                }
            }
        }

        // Initialize the application
        document.addEventListener('DOMContentLoaded', () => {
            try {
                window.idebateApp = new IDEBATEApp();
            } catch (error) {
                console.error('Error initializing application:', error);
                document.getElementById('loading-screen').innerHTML = `
                    <div class="text-center text-white">
                        <h3>Error Loading Application</h3>
                        <p>Please refresh the page or check the console for details.</p>
                        <button class="btn btn-primary mt-3" onclick="location.reload()">Reload Page</button>
                    </div>
                `;
            }
        });
    </script>
</body>
</html>
