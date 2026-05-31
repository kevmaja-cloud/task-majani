<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Task-Majani</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        /* ==================== CSS VARIABLES & THEME SYSTEM ==================== */
        :root {
            --primary: #2563eb;
            --primary-dark: #1d4ed8;
            --primary-light: #dbeafe;
            --secondary: #64748b;
            --success: #10b981;
            --warning: #f59e0b;
            --danger: #ef4444;
            --info: #06b6d4;
            --dark: #1e293b;
            --light: #f8fafc;
            --sidebar-width: 260px;
            --header-height: 64px;
            --transition: all 0.3s ease;
            --bg-body: #f1f5f9;
            --bg-card: white;
            --bg-sidebar: #1e293b;
            --text-primary: #1e293b;
            --text-secondary: #64748b;
            --border-color: #e2e8f0;
            --shadow: 0 1px 3px rgba(0,0,0,0.08);
            --shadow-hover: 0 10px 25px rgba(0,0,0,0.1);
        }

        [data-theme="dark"] {
            --primary: #3b82f6;
            --primary-dark: #2563eb;
            --primary-light: #1e3a5f;
            --secondary: #94a3b8;
            --success: #34d399;
            --warning: #fbbf24;
            --danger: #f87171;
            --info: #22d3ee;
            --dark: #f1f5f9;
            --light: #0f172a;
            --bg-body: #0f172a;
            --bg-card: #1e293b;
            --bg-sidebar: #0f172a;
            --text-primary: #f1f5f9;
            --text-secondary: #94a3b8;
            --border-color: #334155;
            --shadow: 0 1px 3px rgba(0,0,0,0.3);
            --shadow-hover: 0 10px 25px rgba(0,0,0,0.4);
        }

        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: 'Inter', sans-serif;
            background: var(--bg-body);
            color: var(--text-primary);
            overflow-x: hidden;
            transition: var(--transition);
        }

        /* ==================== LOGIN SCREEN ==================== */
        .login-screen {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            display: flex; align-items: center; justify-content: center;
            z-index: 9999; transition: opacity 0.5s ease, visibility 0.5s ease;
        }
        .login-screen.hidden { opacity: 0; visibility: hidden; }
        .login-box {
            background: white; padding: 40px; border-radius: 20px;
            box-shadow: 0 25px 50px -12px rgba(0,0,0,0.25);
            width: 100%; max-width: 420px; text-align: center;
        }
        .login-box .logo { font-size: 48px; color: var(--primary); margin-bottom: 10px; }
        .login-box h1 { font-size: 24px; font-weight: 700; margin-bottom: 8px; }
        .login-box p { color: var(--secondary); margin-bottom: 30px; font-size: 14px; }
        .login-box input {
            width: 100%; padding: 14px 16px; border: 2px solid #e2e8f0;
            border-radius: 12px; font-size: 15px; margin-bottom: 16px;
            transition: var(--transition); font-family: inherit;
        }
        .login-box input:focus { outline: none; border-color: var(--primary); }
        .login-box button {
            width: 100%; padding: 14px; background: var(--primary); color: white;
            border: none; border-radius: 12px; font-size: 16px; font-weight: 600;
            cursor: pointer; transition: var(--transition);
        }
        .login-box button:hover { background: var(--primary-dark); transform: translateY(-2px); }
        .login-error { color: var(--danger); font-size: 13px; margin-top: 10px; display: none; }

        /* ==================== APP LAYOUT ==================== */
        .app-container { display: none; min-height: 100vh; }
        .app-container.active { display: flex; }

        /* ==================== SIDEBAR ==================== */
        .sidebar {
            width: var(--sidebar-width); background: var(--bg-sidebar); color: white;
            position: fixed; height: 100vh; overflow-y: auto;
            transition: var(--transition); z-index: 100;
        }
        .sidebar-header { padding: 24px 20px; border-bottom: 1px solid rgba(255,255,255,0.1); }
        .sidebar-header .brand { display: flex; align-items: center; gap: 12px; font-size: 18px; font-weight: 700; }
        .sidebar-header .brand i { font-size: 28px; color: var(--primary); }
        .sidebar-header .user-info {
            margin-top: 16px; padding-top: 16px;
            border-top: 1px solid rgba(255,255,255,0.1);
            display: flex; align-items: center; gap: 12px;
        }
        .user-avatar {
            width: 40px; height: 40px; border-radius: 50%; background: var(--primary);
            display: flex; align-items: center; justify-content: center;
            font-weight: 600; font-size: 16px; color: white;
        }
        .user-details .name { font-weight: 600; font-size: 14px; }
        .user-details .role { font-size: 12px; color: rgba(255,255,255,0.6); }
        .nav-menu { padding: 16px 12px; }
        .nav-section { margin-bottom: 8px; }
        .nav-section-title {
            font-size: 11px; text-transform: uppercase; letter-spacing: 0.5px;
            color: rgba(255,255,255,0.4); padding: 8px 16px; font-weight: 600;
        }
        .nav-item {
            display: flex; align-items: center; gap: 12px; padding: 12px 16px;
            border-radius: 10px; cursor: pointer; transition: var(--transition);
            margin-bottom: 4px; font-size: 14px; font-weight: 500; position: relative;
            color: rgba(255,255,255,0.9);
        }
        .nav-item:hover { background: rgba(255,255,255,0.08); }
        .nav-item.active { background: var(--primary); }
        .nav-item i { width: 20px; text-align: center; font-size: 16px; }
        .nav-badge {
            position: absolute; right: 12px; background: var(--danger); color: white;
            font-size: 11px; padding: 2px 8px; border-radius: 10px; font-weight: 600;
        }
        .nav-badge.warning { background: var(--warning); }
        .nav-badge.success { background: var(--success); }

        /* ==================== MAIN CONTENT ==================== */
        .main-content { margin-left: var(--sidebar-width); flex: 1; min-height: 100vh; }
        .top-header {
            height: var(--header-height); background: var(--bg-card); border-bottom: 1px solid var(--border-color);
            display: flex; align-items: center; justify-content: space-between;
            padding: 0 28px; position: sticky; top: 0; z-index: 50;
            transition: var(--transition);
        }
        .header-left { display: flex; align-items: center; gap: 16px; }
        .header-left h2 { font-size: 20px; font-weight: 700; color: var(--text-primary); }
        .breadcrumb { display: flex; align-items: center; gap: 8px; font-size: 13px; color: var(--text-secondary); }
        .header-right { display: flex; align-items: center; gap: 16px; }
        .search-box { position: relative; }
        .search-box input {
            padding: 10px 16px 10px 40px; border: 1px solid var(--border-color);
            border-radius: 10px; width: 280px; font-size: 14px; transition: var(--transition);
            background: var(--bg-card); color: var(--text-primary);
        }
        .search-box input:focus { outline: none; border-color: var(--primary); width: 320px; }
        .search-box i { position: absolute; left: 14px; top: 50%; transform: translateY(-50%); color: var(--text-secondary); }
        .header-btn {
            width: 40px; height: 40px; border-radius: 10px; border: 1px solid var(--border-color);
            background: var(--bg-card); cursor: pointer; display: flex; align-items: center; justify-content: center;
            transition: var(--transition); position: relative; color: var(--text-primary);
        }
        .header-btn:hover { background: var(--bg-body); }
        .header-btn .dot { position: absolute; top: 8px; right: 8px; width: 8px; height: 8px; background: var(--danger); border-radius: 50%; }
        .content-area { padding: 28px; }

        /* ==================== STATS & CARDS ==================== */
        .stats-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(240px, 1fr)); gap: 20px; margin-bottom: 28px; }
        .stat-card {
            background: var(--bg-card); border-radius: 16px; padding: 24px;
            box-shadow: var(--shadow); transition: var(--transition);
            position: relative; overflow: hidden;
        }
        .stat-card:hover { transform: translateY(-4px); box-shadow: var(--shadow-hover); }
        .stat-card::before { content: ''; position: absolute; top: 0; left: 0; width: 4px; height: 100%; }
        .stat-card.blue::before { background: var(--primary); }
        .stat-card.green::before { background: var(--success); }
        .stat-card.orange::before { background: var(--warning); }
        .stat-card.red::before { background: var(--danger); }
        .stat-card.purple::before { background: #8b5cf6; }
        .stat-card.cyan::before { background: var(--info); }
        .stat-header { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 12px; }
        .stat-icon {
            width: 48px; height: 48px; border-radius: 12px;
            display: flex; align-items: center; justify-content: center; font-size: 22px;
        }
        .stat-icon.blue { background: var(--primary-light); color: var(--primary); }
        .stat-icon.green { background: #d1fae5; color: var(--success); }
        .stat-icon.orange { background: #fef3c7; color: var(--warning); }
        .stat-icon.red { background: #fee2e2; color: var(--danger); }
        .stat-icon.purple { background: #ede9fe; color: #8b5cf6; }
        .stat-icon.cyan { background: #cffafe; color: var(--info); }
        .stat-value { font-size: 32px; font-weight: 800; color: var(--text-primary); line-height: 1; margin-bottom: 6px; }
        .stat-label { font-size: 14px; color: var(--text-secondary); font-weight: 500; }

        /* ==================== SECTIONS & BUTTONS ==================== */
        .section-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
        .section-title { font-size: 18px; font-weight: 700; display: flex; align-items: center; gap: 10px; color: var(--text-primary); }
        .section-title i { color: var(--primary); }
        .section-actions { display: flex; gap: 10px; }
        .btn {
            padding: 10px 20px; border-radius: 10px; font-size: 14px; font-weight: 600;
            cursor: pointer; border: none; display: inline-flex; align-items: center; gap: 8px;
            transition: var(--transition);
        }
        .btn-primary { background: var(--primary); color: white; }
        .btn-primary:hover { background: var(--primary-dark); }
        .btn-outline { background: var(--bg-card); color: var(--text-primary); border: 1px solid var(--border-color); }
        .btn-outline:hover { background: var(--bg-body); }
        .btn-danger { background: var(--danger); color: white; }
        .btn-danger:hover { background: #dc2626; }
        .btn-success { background: var(--success); color: white; }
        .btn-warning { background: var(--warning); color: white; }
        .btn-sm { padding: 6px 12px; font-size: 12px; }
        .btn-icon { padding: 8px; border-radius: 8px; }

        /* ==================== CARDS & TABLES ==================== */
        .card {
            background: var(--bg-card); border-radius: 16px; box-shadow: var(--shadow);
            overflow: hidden; margin-bottom: 24px; transition: var(--transition);
            border: 1px solid var(--border-color);
        }
        .card-header {
            padding: 20px 24px; border-bottom: 1px solid var(--border-color);
            display: flex; justify-content: space-between; align-items: center;
        }
        .card-header h3 { font-size: 16px; font-weight: 700; display: flex; align-items: center; gap: 10px; color: var(--text-primary); }
        .card-body { padding: 20px 24px; }
        .data-table { width: 100%; border-collapse: collapse; }
        .data-table th {
            text-align: left; padding: 14px 16px; font-size: 12px; font-weight: 600;
            text-transform: uppercase; letter-spacing: 0.5px; color: var(--text-secondary);
            background: var(--bg-body); border-bottom: 1px solid var(--border-color); white-space: nowrap;
        }
        .data-table td { padding: 14px 16px; font-size: 14px; border-bottom: 1px solid var(--border-color); vertical-align: middle; color: var(--text-primary); }
        .data-table tr:hover td { background: var(--bg-body); }
        .data-table tr:last-child td { border-bottom: none; }

        /* ==================== BADGES ==================== */
        .badge {
            display: inline-flex; align-items: center; gap: 6px;
            padding: 6px 12px; border-radius: 20px; font-size: 12px; font-weight: 600;
        }
        .badge-high { background: #fee2e2; color: #991b1b; }
        .badge-medium { background: #fef3c7; color: #92400e; }
        .badge-low { background: #d1fae5; color: #065f46; }
        .badge-critical { background: #fecaca; color: #7f1d1d; }
        .badge-progress { background: #dbeafe; color: #1e40af; }
        .badge-done { background: #d1fae5; color: #065f46; }
        .badge-not-started { background: var(--bg-body); color: var(--text-secondary); }
        .badge-overdue { background: #fee2e2; color: #991b1b; }
        .badge-new { background: #e0e7ff; color: #3730a3; }
        .badge-contacted { background: #dbeafe; color: #1e40af; }
        .badge-interested { background: #d1fae5; color: #065f46; }
        .badge-proposal { background: #fef3c7; color: #92400e; }
        .badge-negotiation { background: #fce7f3; color: #9d174d; }
        .badge-followup { background: #e0e7ff; color: #3730a3; }
        .badge-won { background: #d1fae5; color: #065f46; }
        .badge-lost { background: #fee2e2; color: #991b1b; }
        .badge-dormant { background: #f3f4f6; color: #6b7280; }
        .badge-converted { background: #c7d2fe; color: #312e81; }
        .badge-archived { background: #e5e7eb; color: #374151; }

        /* ==================== PROGRESS BARS ==================== */
        .progress-bar { width: 100%; height: 8px; background: var(--bg-body); border-radius: 4px; overflow: hidden; }
        .progress-fill { height: 100%; border-radius: 4px; transition: width 0.5s ease; }
        .progress-fill.blue { background: var(--primary); }
        .progress-fill.green { background: var(--success); }
        .progress-fill.orange { background: var(--warning); }
        .progress-fill.red { background: var(--danger); }
        .progress-text { font-size: 12px; font-weight: 600; color: var(--text-secondary); margin-top: 4px; }

        /* ==================== CHARTS ==================== */
        .charts-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(400px, 1fr)); gap: 24px; margin-bottom: 24px; }
        .chart-container { position: relative; height: 300px; }

        /* ==================== RISK & HEALTH ==================== */
        .risk-critical { color: #dc2626; font-weight: 700; }
        .risk-high { color: #ea580c; font-weight: 700; }
        .risk-medium { color: #ca8a04; font-weight: 700; }
        .risk-low { color: #16a34a; font-weight: 700; }
        .health-healthy { color: var(--success); }
        .health-watch { color: var(--warning); }
        .health-risk { color: var(--danger); }

        /* ==================== MODALS ==================== */
        .modal-overlay {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.5); display: none; align-items: center; justify-content: center;
            z-index: 200;
        }
        .modal-overlay.active { display: flex; }
        .modal {
            background: var(--bg-card); border-radius: 20px; width: 90%; max-width: 700px;
            max-height: 90vh; overflow-y: auto; animation: modalSlide 0.3s ease;
            border: 1px solid var(--border-color);
        }
        .modal-lg { max-width: 900px; }
        @keyframes modalSlide {
            from { opacity: 0; transform: translateY(-30px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .modal-header { padding: 24px; border-bottom: 1px solid var(--border-color); display: flex; justify-content: space-between; align-items: center; }
        .modal-header h3 { font-size: 18px; font-weight: 700; color: var(--text-primary); }
        .modal-close { width: 36px; height: 36px; border-radius: 10px; border: none; background: var(--bg-body); cursor: pointer; font-size: 18px; transition: var(--transition); color: var(--text-primary); }
        .modal-close:hover { background: var(--border-color); }
        .modal-body { padding: 24px; }
        .form-group { margin-bottom: 20px; }
        .form-group label { display: block; font-size: 14px; font-weight: 600; margin-bottom: 8px; color: var(--text-primary); }
        .form-group input, .form-group select, .form-group textarea {
            width: 100%; padding: 12px 16px; border: 2px solid var(--border-color);
            border-radius: 10px; font-size: 14px; font-family: inherit; transition: var(--transition);
            background: var(--bg-card); color: var(--text-primary);
        }
        .form-group input:focus, .form-group select:focus, .form-group textarea:focus { outline: none; border-color: var(--primary); }
        .form-group textarea { resize: vertical; min-height: 100px; }
        .form-row { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; }
        .form-row-3 { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 16px; }

        /* ==================== PAGE SECTIONS ==================== */
        .page-section { display: none; }
        .page-section.active { display: block; animation: fadeIn 0.3s ease; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        /* ==================== FILTERS ==================== */
        .filter-bar { display: flex; gap: 12px; margin-bottom: 20px; flex-wrap: wrap; }
        .filter-bar select, .filter-bar input {
            padding: 10px 16px; border: 1px solid var(--border-color);
            border-radius: 10px; font-size: 14px; background: var(--bg-card); cursor: pointer;
            color: var(--text-primary);
        }

        /* ==================== QUICK ACTIONS ==================== */
        .quick-actions { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 16px; margin-bottom: 28px; }
        .quick-action-card {
            background: var(--bg-card); border-radius: 16px; padding: 20px; cursor: pointer;
            transition: var(--transition); border: 2px solid transparent;
            display: flex; align-items: center; gap: 16px;
        }
        .quick-action-card:hover { border-color: var(--primary); transform: translateY(-2px); box-shadow: var(--shadow-hover); }
        .quick-action-icon { width: 50px; height: 50px; border-radius: 14px; display: flex; align-items: center; justify-content: center; font-size: 22px; }
        .quick-action-info h4 { font-size: 15px; font-weight: 700; margin-bottom: 4px; color: var(--text-primary); }
        .quick-action-info p { font-size: 13px; color: var(--text-secondary); }
        .quick-action-count { margin-left: auto; font-size: 24px; font-weight: 800; color: var(--primary); }

        /* ==================== NOTIFICATIONS ==================== */
        .notification {
            position: fixed; top: 20px; right: 20px; padding: 16px 24px;
            border-radius: 12px; color: white; font-weight: 600; z-index: 300;
            transform: translateX(400px); transition: transform 0.3s ease;
            box-shadow: 0 10px 25px rgba(0,0,0,0.2);
        }
        .notification.show { transform: translateX(0); }
        .notification.success { background: var(--success); }
        .notification.error { background: var(--danger); }
        .notification.info { background: var(--primary); }
        .notification.warning { background: var(--warning); }

        /* ==================== ALERTS PANEL ==================== */
        .alerts-panel {
            position: fixed; top: var(--header-height); right: 0; width: 380px;
            height: calc(100vh - var(--header-height)); background: var(--bg-card);
            border-left: 1px solid var(--border-color); z-index: 150;
            transform: translateX(100%); transition: transform 0.3s ease;
            overflow-y: auto; box-shadow: -5px 0 25px rgba(0,0,0,0.1);
        }
        .alerts-panel.active { transform: translateX(0); }
        .alerts-header { padding: 20px 24px; border-bottom: 1px solid var(--border-color); display: flex; justify-content: space-between; align-items: center; }
        .alerts-header h3 { font-size: 16px; font-weight: 700; color: var(--text-primary); }
        .alert-item { padding: 16px 24px; border-bottom: 1px solid var(--border-color); display: flex; gap: 12px; align-items: flex-start; }
        .alert-item:last-child { border-bottom: none; }
        .alert-icon { width: 36px; height: 36px; border-radius: 10px; display: flex; align-items: center; justify-content: center; font-size: 16px; flex-shrink: 0; }
        .alert-icon.danger { background: #fee2e2; color: var(--danger); }
        .alert-icon.warning { background: #fef3c7; color: var(--warning); }
        .alert-icon.info { background: #dbeafe; color: var(--primary); }
        .alert-content { flex: 1; }
        .alert-title { font-weight: 600; font-size: 14px; color: var(--text-primary); margin-bottom: 4px; }
        .alert-desc { font-size: 13px; color: var(--text-secondary); }
        .alert-time { font-size: 11px; color: var(--text-secondary); margin-top: 4px; }

        /* ==================== TIMELINE ==================== */
        .timeline { position: relative; padding-left: 24px; }
        .timeline::before {
            content: ''; position: absolute; left: 8px; top: 0; bottom: 0;
            width: 2px; background: var(--border-color);
        }
        .timeline-item { position: relative; padding-bottom: 20px; }
        .timeline-item:last-child { padding-bottom: 0; }
        .timeline-dot {
            position: absolute; left: -20px; top: 4px; width: 12px; height: 12px;
            border-radius: 50%; background: var(--primary); border: 2px solid var(--bg-card);
        }
        .timeline-dot.call { background: var(--info); }
        .timeline-dot.note { background: var(--warning); }
        .timeline-dot.email { background: var(--success); }
        .timeline-dot.meeting { background: var(--danger); }
        .timeline-content { background: var(--bg-body); padding: 12px 16px; border-radius: 12px; }
        .timeline-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 6px; }
        .timeline-date { font-size: 12px; font-weight: 600; color: var(--text-secondary); }
        .timeline-user { font-size: 12px; color: var(--primary); font-weight: 600; }
        .timeline-text { font-size: 14px; color: var(--text-primary); }
        .timeline-type { font-size: 11px; padding: 2px 8px; border-radius: 6px; font-weight: 600; margin-left: 8px; }
        .timeline-type.call { background: #cffafe; color: #0891b2; }
        .timeline-type.note { background: #fef3c7; color: #92400e; }
        .timeline-type.email { background: #d1fae5; color: #065f46; }
        .timeline-type.meeting { background: #fee2e2; color: #991b1b; }

        /* ==================== LEAD PIPELINE ==================== */
        .pipeline-container { display: flex; gap: 12px; overflow-x: auto; padding-bottom: 16px; margin-bottom: 20px; }
        .pipeline-stage {
            min-width: 180px; background: var(--bg-body); border-radius: 12px;
            padding: 16px; border: 2px solid var(--border-color);
        }
        .pipeline-stage-header {
            display: flex; justify-content: space-between; align-items: center;
            margin-bottom: 12px; font-weight: 600; font-size: 13px; color: var(--text-primary);
        }
        .pipeline-count { background: var(--bg-card); padding: 2px 10px; border-radius: 10px; font-size: 12px; }
        .pipeline-card {
            background: var(--bg-card); padding: 12px; border-radius: 10px;
            margin-bottom: 8px; cursor: pointer; transition: var(--transition);
            border: 1px solid var(--border-color);
        }
        .pipeline-card:hover { transform: translateY(-2px); box-shadow: var(--shadow); border-color: var(--primary); }
        .pipeline-card-title { font-weight: 600; font-size: 13px; margin-bottom: 6px; color: var(--text-primary); }
        .pipeline-card-meta { font-size: 11px; color: var(--text-secondary); display: flex; gap: 8px; }

        /* ==================== CALENDAR ==================== */
        .calendar-grid {
            display: grid; grid-template-columns: repeat(7, 1fr); gap: 1px;
            background: var(--border-color); border-radius: 12px; overflow: hidden;
        }
        .calendar-header-cell {
            background: var(--bg-body); padding: 12px; text-align: center;
            font-weight: 600; font-size: 13px; color: var(--text-secondary);
        }
        .calendar-day {
            background: var(--bg-card); min-height: 120px; padding: 8px;
            position: relative; cursor: pointer; transition: var(--transition);
        }
        .calendar-day:hover { background: var(--bg-body); }
        .calendar-day.other-month { background: var(--bg-body); opacity: 0.5; }
        .calendar-day-number { font-weight: 600; font-size: 14px; margin-bottom: 4px; color: var(--text-primary); }
        .calendar-day.today .calendar-day-number { color: var(--primary); }
        .calendar-event {
            font-size: 10px; padding: 2px 6px; border-radius: 4px; margin-bottom: 2px;
            white-space: nowrap; overflow: hidden; text-overflow: ellipsis;
        }
        .calendar-event.task { background: var(--primary-light); color: var(--primary); }
        .calendar-event.lead { background: #fef3c7; color: #92400e; }
        .calendar-event.goal { background: #d1fae5; color: #065f46; }
        .calendar-event.overdue { background: #fee2e2; color: #991b1b; }

        /* ==================== GOAL TRACKER ==================== */
        .goal-card {
            background: var(--bg-card); border-radius: 16px; padding: 24px;
            border: 1px solid var(--border-color); margin-bottom: 20px;
        }
        .goal-header { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 16px; }
        .goal-title { font-size: 16px; font-weight: 700; color: var(--text-primary); }
        .goal-stats { display: grid; grid-template-columns: repeat(4, 1fr); gap: 16px; margin-bottom: 16px; }
        .goal-stat { text-align: center; }
        .goal-stat-value { font-size: 20px; font-weight: 700; color: var(--primary); }
        .goal-stat-label { font-size: 11px; color: var(--text-secondary); margin-top: 2px; }
        .goal-progress { margin-bottom: 16px; }
        .goal-forecast { font-size: 13px; color: var(--text-secondary); padding: 12px; background: var(--bg-body); border-radius: 10px; }

        /* ==================== FILE UPLOAD ==================== */
        .file-dropzone {
            border: 2px dashed var(--border-color); border-radius: 12px;
            padding: 40px; text-align: center; cursor: pointer; transition: var(--transition);
            background: var(--bg-body);
        }
        .file-dropzone:hover { border-color: var(--primary); background: var(--primary-light); }
        .file-dropzone i { font-size: 32px; color: var(--primary); margin-bottom: 12px; }
        .file-list { margin-top: 16px; }
        .file-item {
            display: flex; align-items: center; gap: 12px; padding: 10px 16px;
            background: var(--bg-body); border-radius: 10px; margin-bottom: 8px;
        }
        .file-item i { color: var(--primary); }
        .file-item-name { flex: 1; font-size: 13px; color: var(--text-primary); }
        .file-item-size { font-size: 11px; color: var(--text-secondary); }

        /* ==================== TABS ==================== */
        .tabs { display: flex; gap: 4px; border-bottom: 1px solid var(--border-color); margin-bottom: 20px; }
        .tab {
            padding: 12px 20px; font-size: 14px; font-weight: 600; cursor: pointer;
            border-bottom: 2px solid transparent; transition: var(--transition);
            color: var(--text-secondary);
        }
        .tab:hover { color: var(--primary); }
        .tab.active { color: var(--primary); border-bottom-color: var(--primary); }
        .tab-content { display: none; }
        .tab-content.active { display: block; animation: fadeIn 0.3s ease; }

        /* ==================== ROLE BADGE ==================== */
        .role-badge {
            display: inline-flex; align-items: center; gap: 4px;
            padding: 4px 10px; border-radius: 6px; font-size: 11px; font-weight: 600;
        }
        .role-admin { background: #fee2e2; color: #991b1b; }
        .role-manager { background: #fef3c7; color: #92400e; }
        .role-staff { background: #dbeafe; color: #1e40af; }
        .role-viewer { background: #f3f4f6; color: #6b7280; }


        /* ==================== CUSTOM LOGO ==================== */
        .logo-img {
            width: 48px;
            height: 48px;
            border-radius: 12px;
            object-fit: cover;
            margin-bottom: 10px;
        }
        .brand-logo {
            width: 32px;
            height: 32px;
            border-radius: 8px;
            object-fit: cover;
            margin-right: 8px;
        }

        /* ==================== SCROLLBAR ==================== */
        ::-webkit-scrollbar { width: 8px; height: 8px; }
        ::-webkit-scrollbar-track { background: var(--bg-body); }
        ::-webkit-scrollbar-thumb { background: var(--border-color); border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: var(--text-secondary); }

        /* ==================== RESPONSIVE ==================== */
        @media (max-width: 768px) {
            .sidebar { transform: translateX(-100%); }
            .sidebar.open { transform: translateX(0); }
            .main-content { margin-left: 0; }
            .charts-grid { grid-template-columns: 1fr; }
            .form-row, .form-row-3 { grid-template-columns: 1fr; }
            .search-box input { width: 200px; }
            .search-box input:focus { width: 220px; }
            .alerts-panel { width: 100%; }
        }

        /* ==================== UTILITY ==================== */
        .text-muted { color: var(--text-secondary); }
        .text-success { color: var(--success); }
        .text-danger { color: var(--danger); }
        .text-warning { color: var(--warning); }
        .text-primary { color: var(--primary); }
        .fw-bold { font-weight: 700; }
        .fs-sm { font-size: 13px; }
        .mb-0 { margin-bottom: 0; }
        .mt-2 { margin-top: 8px; }
        .d-flex { display: flex; }
        .align-center { align-items: center; }
        .gap-2 { gap: 8px; }
        .gap-3 { gap: 12px; }
        .flex-wrap { flex-wrap: wrap; }
        .justify-between { justify-content: space-between; }
        .w-100 { width: 100%; }
    </style>
<base target="_blank">
</head>

<body>
    <!-- ==================== LOGIN SCREEN ==================== -->
    <div class="login-screen" id="loginScreen">
        <div class="login-box">
            <!-- TO ADD YOUR OWN LOGO: Replace the line below with:
     <img src="your-logo.png" class="logo-img" alt="Task-Majani Logo">
     Upload your logo to the same folder as this HTML file. -->
     <div class="logo"><i class="fas fa-tasks"></i></div>
            <h1>Task-Majani</h1>
            <p>Your Personal Task & Goal Manager</p>
            <input type="text" id="loginUsername" placeholder="Username" value="admin">
            <input type="password" id="loginPassword" placeholder="Password" value="admin123">
            <button onclick="handleLogin()">Sign In</button>
            <div class="login-error" id="loginError">Invalid credentials. Try admin / admin123</div>
            <p style="margin-top: 20px; font-size: 12px; color: #94a3b8;">
                <i class="fas fa-info-circle"></i> Demo accounts: admin/admin123, manager/manager123, staff/staff123, viewer/viewer123
            </p>
        </div>
    </div>

    <!-- ==================== MAIN APP ==================== -->
    <div class="app-container" id="appContainer">
        <aside class="sidebar" id="sidebar">
            <div class="sidebar-header">
                <div class="brand"><i class="fas fa-tasks"></i><span>Task-Majani</span></div>
                <div class="user-info">
                    <div class="user-avatar" id="userAvatar">A</div>
                    <div class="user-details">
                        <div class="name" id="userName">Admin</div>
                        <div class="role" id="userRole">System Administrator</div>
                        <span class="role-badge role-admin" id="userRoleBadge">Admin</span>
                    </div>
                </div>
            </div>
            <nav class="nav-menu">
                <div class="nav-section">
                    <div class="nav-section-title">Main</div>
                    <div class="nav-item active" onclick="showPage('dashboard')">
                        <i class="fas fa-chart-line"></i><span>Dashboard</span>
                    </div>
                    <div class="nav-item" onclick="showPage('tasks')">
                        <i class="fas fa-list-check"></i><span>All Tasks</span>
                        <span class="nav-badge" id="navTotalTasks">0</span>
                    </div>
                    <div class="nav-item" onclick="showPage('overdue')">
                        <i class="fas fa-exclamation-triangle"></i><span>Overdue Alerts</span>
                        <span class="nav-badge" id="navOverdue">0</span>
                    </div>
                    <div class="nav-item" onclick="showPage('workload')">
                        <i class="fas fa-users"></i><span>Team Workload</span>
                        <span class="nav-badge warning" id="navWorkload">0</span>
                    </div>
                </div>
                <div class="nav-section">
                    <div class="nav-section-title">CRM</div>
                    <div class="nav-item" onclick="showPage('leads')">
                        <i class="fas fa-address-book"></i><span>Lead Tracker</span>
                        <span class="nav-badge" id="navLeads">0</span>
                    </div>
                    <div class="nav-item" onclick="showPage('pipeline')">
                        <i class="fas fa-filter"></i><span>Sales Pipeline</span>
                    </div>
                    <div class="nav-item" onclick="showPage('activities')">
                        <i class="fas fa-history"></i><span>Activities</span>
                        <span class="nav-badge success" id="navActivities">0</span>
                    </div>
                </div>
                <div class="nav-section">
                    <div class="nav-section-title">Management</div>
                    <div class="nav-item" onclick="showPage('projects')">
                        <i class="fas fa-project-diagram"></i><span>Projects</span>
                        <span class="nav-badge" id="navProjects">0</span>
                    </div>
                    <div class="nav-item" onclick="showPage('risk')">
                        <i class="fas fa-bolt"></i><span>Risk Engine</span>
                    </div>
                    <div class="nav-item" onclick="showPage('evm')">
                        <i class="fas fa-dollar-sign"></i><span>EVM Financials</span>
                    </div>
                    <div class="nav-item" onclick="showPage('goals')">
                        <i class="fas fa-bullseye"></i><span>Goal Tracker</span>
                    </div>
                    <div class="nav-item" onclick="showPage('calendar')">
                        <i class="fas fa-calendar"></i><span>Calendar</span>
                    </div>
                </div>
                <div class="nav-section">
                    <div class="nav-section-title">Analytics</div>
                    <div class="nav-item" onclick="showPage('monthly')">
                        <i class="fas fa-calendar-alt"></i><span>Monthly Analytics</span>
                    </div>
                    <div class="nav-item" onclick="showPage('yearly')">
                        <i class="fas fa-chart-bar"></i><span>Yearly Trends</span>
                    </div>
                    <div class="nav-item" onclick="showPage('performance')">
                        <i class="fas fa-chart-pie"></i><span>Performance Charts</span>
                    </div>
                </div>
                <div class="nav-section">
                    <div class="nav-section-title">System</div>
                    <div class="nav-item" onclick="showPage('importexport')">
                        <i class="fas fa-file-import"></i><span>Import / Export</span>
                    </div>
                    <div class="nav-item" onclick="toggleTheme()">
                        <i class="fas fa-moon" id="themeIcon"></i><span id="themeText">Dark Mode</span>
                    </div>
                    <div class="nav-item" onclick="handleLogout()">
                        <i class="fas fa-sign-out-alt"></i><span>Logout</span>
                    </div>
                </div>
            </nav>
        </aside>

        <main class="main-content">
            <header class="top-header">
                <div class="header-left">
                    <h2 id="pageTitle">Dashboard</h2>
                    <div class="breadcrumb">
                        <span>Home</span>
                        <i class="fas fa-chevron-right" style="font-size:10px;"></i>
                        <span id="breadcrumbPage">Dashboard</span>
                    </div>
                </div>
                <div class="header-right">
                    <div class="search-box">
                        <i class="fas fa-search"></i>
                        <input type="text" id="globalSearch" placeholder="Search..." onkeyup="handleSearch()">
                    </div>
                    <button class="header-btn" onclick="toggleAlertsPanel()" title="Alerts">
                        <i class="fas fa-bell"></i><span class="dot" id="notifDot" style="display:none;"></span>
                    </button>
                    <button class="header-btn" onclick="showPage('goals')" title="Goals">
                        <i class="fas fa-bullseye"></i>
                    </button>
                </div>
            </header>

            <div class="content-area">

                <!-- ==================== DASHBOARD ==================== -->
                <div class="page-section active" id="page-dashboard">
                    <div class="stats-grid">
                        <div class="stat-card blue">
                            <div class="stat-header"><div class="stat-icon blue"><i class="fas fa-tasks"></i></div></div>
                            <div class="stat-value" id="dashTotal">0</div><div class="stat-label">Total Tasks</div>
                        </div>
                        <div class="stat-card green">
                            <div class="stat-header"><div class="stat-icon green"><i class="fas fa-spinner"></i></div></div>
                            <div class="stat-value" id="dashProgress">0</div><div class="stat-label">In Progress</div>
                        </div>
                        <div class="stat-card purple">
                            <div class="stat-header"><div class="stat-icon purple"><i class="fas fa-check-circle"></i></div></div>
                            <div class="stat-value" id="dashDone">0</div><div class="stat-label">Completed</div>
                        </div>
                        <div class="stat-card orange">
                            <div class="stat-header"><div class="stat-icon orange"><i class="fas fa-clock"></i></div></div>
                            <div class="stat-value" id="dashNotStarted">0</div><div class="stat-label">Not Started</div>
                        </div>
                        <div class="stat-card red">
                            <div class="stat-header"><div class="stat-icon red"><i class="fas fa-exclamation-circle"></i></div></div>
                            <div class="stat-value" id="dashOverdue">0</div><div class="stat-label">Overdue</div>
                        </div>
                        <div class="stat-card cyan">
                            <div class="stat-header"><div class="stat-icon cyan"><i class="fas fa-flag"></i></div></div>
                            <div class="stat-value" id="dashHigh">0</div><div class="stat-label">High/Critical Priority</div>
                        </div>
                    </div>

                    <!-- Dashboard Widgets Row -->
                    <div class="stats-grid" style="margin-bottom:20px;">
                        <div class="stat-card" style="border-left:4px solid var(--primary);">
                            <div class="stat-header"><div class="stat-icon blue"><i class="fas fa-calendar-day"></i></div></div>
                            <div class="stat-value" id="dashTodayTasks">0</div><div class="stat-label">Today's Tasks</div>
                        </div>
                        <div class="stat-card" style="border-left:4px solid var(--warning);">
                            <div class="stat-header"><div class="stat-icon orange"><i class="fas fa-phone"></i></div></div>
                            <div class="stat-value" id="dashFollowups">0</div><div class="stat-label">Follow-ups Due</div>
                        </div>
                        <div class="stat-card" style="border-left:4px solid var(--success);">
                            <div class="stat-header"><div class="stat-icon green"><i class="fas fa-bullseye"></i></div></div>
                            <div class="stat-value" id="dashGoals">0</div><div class="stat-label">Active Goals</div>
                        </div>
                        <div class="stat-card" style="border-left:4px solid var(--danger);">
                            <div class="stat-header"><div class="stat-icon red"><i class="fas fa-project-diagram"></i></div></div>
                            <div class="stat-value" id="dashOverdueProjects">0</div><div class="stat-label">Overdue Projects</div>
                        </div>
                    </div>

                    <div class="quick-actions">
                        <div class="quick-action-card" onclick="showPage('tasks')">
                            <div class="quick-action-icon" style="background:#dbeafe;color:#2563eb;"><i class="fas fa-plus"></i></div>
                            <div class="quick-action-info"><h4>View All Tasks</h4><p>Manage your task list</p></div>
                            <div class="quick-action-count"><i class="fas fa-arrow-right"></i></div>
                        </div>
                        <div class="quick-action-card" onclick="showPage('overdue')">
                            <div class="quick-action-icon" style="background:#fee2e2;color:#dc2626;"><i class="fas fa-bell"></i></div>
                            <div class="quick-action-info"><h4>Overdue Alerts</h4><p id="dashOverdueQuickText">0 tasks need attention</p></div>
                            <div class="quick-action-count" id="dashOverdueQuickCount" style="color:#dc2626;">0</div>
                        </div>
                        <div class="quick-action-card" onclick="showPage('workload')">
                            <div class="quick-action-icon" style="background:#fef3c7;color:#d97706;"><i class="fas fa-user-clock"></i></div>
                            <div class="quick-action-info"><h4>Team Workload</h4><p id="dashWorkloadText">0 team members</p></div>
                            <div class="quick-action-count" id="dashWorkloadCount" style="color:#d97706;">0</div>
                        </div>
                        <div class="quick-action-card" onclick="showPage('leads')">
                            <div class="quick-action-icon" style="background:#e0e7ff;color:#3730a3;"><i class="fas fa-address-book"></i></div>
                            <div class="quick-action-info"><h4>Lead Tracker</h4><p id="dashLeadsText">0 active leads</p></div>
                            <div class="quick-action-count" id="dashLeadsCount" style="color:#3730a3;">0</div>
                        </div>
                        <div class="quick-action-card" onclick="showPage('goals')">
                            <div class="quick-action-icon" style="background:#d1fae5;color:#065f46;"><i class="fas fa-bullseye"></i></div>
                            <div class="quick-action-info"><h4>Goal Tracker</h4><p>Track daily progress</p></div>
                            <div class="quick-action-count" style="color:#065f46;"><i class="fas fa-arrow-right"></i></div>
                        </div>
                        <div class="quick-action-card" onclick="showPage('calendar')">
                            <div class="quick-action-icon" style="background:#cffafe;color:#0891b2;"><i class="fas fa-calendar"></i></div>
                            <div class="quick-action-info"><h4>Calendar View</h4><p>Schedule & deadlines</p></div>
                            <div class="quick-action-count" style="color:#0891b2;"><i class="fas fa-arrow-right"></i></div>
                        </div>
                    </div>

                    <div class="charts-grid">
                        <div class="card">
                            <div class="card-header"><h3><i class="fas fa-chart-pie"></i> Status Distribution</h3></div>
                            <div class="card-body"><div class="chart-container"><canvas id="statusChart"></canvas></div></div>
                        </div>
                        <div class="card">
                            <div class="card-header"><h3><i class="fas fa-chart-bar"></i> Priority Distribution</h3></div>
                            <div class="card-body"><div class="chart-container"><canvas id="priorityChart"></canvas></div></div>
                        </div>
                    </div>

                    <div class="card">
                        <div class="card-header">
                            <h3><i class="fas fa-exclamation-triangle"></i> Top Overdue Tasks</h3>
                            <button class="btn btn-outline" onclick="showPage('overdue')">View All</button>
                        </div>
                        <div class="card-body"><table class="data-table"><thead><tr><th>Task</th><th>Owner</th><th>Priority</th><th>Days Overdue</th><th>Status</th></tr></thead><tbody id="dashOverdueTable"></tbody></table></div>
                    </div>

                    <div class="card">
                        <div class="card-header">
                            <h3><i class="fas fa-history"></i> Recent Activities</h3>
                            <button class="btn btn-outline" onclick="showPage('activities')">View All</button>
                        </div>
                        <div class="card-body"><div id="dashRecentActivities"></div></div>
                    </div>
                </div>

                <!-- ==================== TASKS ==================== -->
                <div class="page-section" id="page-tasks">
                    <div class="section-header">
                        <div class="section-title"><i class="fas fa-list-check"></i> All Tasks</div>
                        <div class="section-actions">
                            <button class="btn btn-primary" onclick="openAddTaskModal()"><i class="fas fa-plus"></i> Add Task</button>
                            <button class="btn btn-outline" onclick="exportTasks()"><i class="fas fa-download"></i> Export</button>
                        </div>
                    </div>
                    <div class="filter-bar">
                        <select id="filterStatus" onchange="filterTasks()"><option value="">All Statuses</option><option value="In Progress">In Progress</option><option value="Done">Done</option><option value="Not Started">Not Started</option><option value="Blocked">Blocked</option></select>
                        <select id="filterPriority" onchange="filterTasks()"><option value="">All Priorities</option><option value="Critical">Critical</option><option value="High">High</option><option value="Medium">Medium</option><option value="Low">Low</option></select>
                        <select id="filterOwner" onchange="filterTasks()"><option value="">All Owners</option></select>
                        <select id="filterProject" onchange="filterTasks()"><option value="">All Projects</option></select>
                        <select id="filterDueDate" onchange="filterTasks()"><option value="">All Due Dates</option><option value="overdue">Overdue</option><option value="today">Due Today</option><option value="week">This Week</option><option value="month">This Month</option></select>
                    </div>
                    <div class="card"><div class="card-body" style="padding:0;"><div style="overflow-x:auto;"><table class="data-table"><thead><tr><th>Task ID</th><th>Project</th><th>Task Name</th><th>Owner</th><th>Status</th><th>Priority</th><th>Due Date</th><th>% Complete</th><th>Signal</th><th>Actions</th></tr></thead><tbody id="tasksTableBody"></tbody></table></div></div></div>
                </div>

                <!-- ==================== OVERDUE ==================== -->
                <div class="page-section" id="page-overdue">
                    <div class="section-header">
                        <div class="section-title"><i class="fas fa-exclamation-triangle"></i> Overdue Tasks Alert Center</div>
                        <div class="section-actions">
                            <button class="btn btn-danger" onclick="sendOverdueReminders()"><i class="fas fa-paper-plane"></i> Send Reminders</button>
                        </div>
                    </div>
                    <div class="stats-grid" style="margin-bottom:20px;">
                        <div class="stat-card red"><div class="stat-header"><div class="stat-icon red"><i class="fas fa-fire"></i></div></div><div class="stat-value" id="overdueTotal">0</div><div class="stat-label">Total Overdue</div></div>
                        <div class="stat-card orange"><div class="stat-header"><div class="stat-icon orange"><i class="fas fa-calendar-day"></i></div></div><div class="stat-value" id="overdueMax">0</div><div class="stat-label">Max Days Overdue</div></div>
                        <div class="stat-card purple"><div class="stat-header"><div class="stat-icon purple"><i class="fas fa-users"></i></div></div><div class="stat-value" id="overdueOwners">0</div><div class="stat-label">Affected Owners</div></div>
                    </div>
                    <div class="card"><div class="card-header"><h3><i class="fas fa-list"></i> Overdue Task List</h3></div><div class="card-body" style="padding:0;"><div style="overflow-x:auto;"><table class="data-table"><thead><tr><th>Task ID</th><th>Task Name</th><th>Owner</th><th>Project</th><th>Priority</th><th>Days Overdue</th><th>Due Date</th><th>% Complete</th><th>Status</th></tr></thead><tbody id="overdueTableBody"></tbody></table></div></div></div>
                </div>

                <!-- ==================== WORKLOAD ==================== -->
                <div class="page-section" id="page-workload">
                    <div class="section-header"><div class="section-title"><i class="fas fa-users"></i> Team Workload Analytics</div></div>
                    <div class="card"><div class="card-header"><h3><i class="fas fa-table"></i> Owner Workload Distribution</h3></div><div class="card-body" style="padding:0;"><div style="overflow-x:auto;"><table class="data-table"><thead><tr><th>Owner</th><th>Total Tasks</th><th>In Progress</th><th>Done</th><th>Avg % Complete</th><th>Overdue</th><th>High Priority</th><th>Status</th></tr></thead><tbody id="workloadTableBody"></tbody></table></div></div></div>
                    <div class="charts-grid" style="margin-top:24px;">
                        <div class="card"><div class="card-header"><h3><i class="fas fa-chart-bar"></i> Tasks per Owner</h3></div><div class="card-body"><div class="chart-container"><canvas id="workloadChart"></canvas></div></div></div>
                        <div class="card"><div class="card-header"><h3><i class="fas fa-chart-pie"></i> Workload Distribution</h3></div><div class="card-body"><div class="chart-container"><canvas id="workloadPieChart"></canvas></div></div></div>
                    </div>
                </div>

                <!-- ==================== MONTHLY ==================== -->
                <div class="page-section" id="page-monthly">
                    <div class="section-header"><div class="section-title"><i class="fas fa-calendar-alt"></i> Monthly Analytics Dashboard</div></div>
                    <div class="card"><div class="card-header"><h3><i class="fas fa-table"></i> Monthly Breakdown</h3></div><div class="card-body" style="padding:0;"><div style="overflow-x:auto;"><table class="data-table"><thead><tr><th>Month</th><th>Year</th><th>Tasks Created</th><th>Completed</th><th>In Progress</th><th>Overdue</th><th>Blocked</th><th>Completion Rate</th></tr></thead><tbody id="monthlyTableBody"></tbody></table></div></div></div>
                    <div class="card" style="margin-top:24px;"><div class="card-header"><h3><i class="fas fa-chart-line"></i> Monthly Task Creation Trend</h3></div><div class="card-body"><div class="chart-container"><canvas id="monthlyChart"></canvas></div></div></div>
                </div>

                <!-- ==================== YEARLY ==================== -->
                <div class="page-section" id="page-yearly">
                    <div class="section-header"><div class="section-title"><i class="fas fa-chart-bar"></i> Yearly Trends & Comparisons</div></div>
                    <div class="card"><div class="card-header"><h3><i class="fas fa-table"></i> Year-over-Year Performance</h3></div><div class="card-body" style="padding:0;"><div style="overflow-x:auto;"><table class="data-table"><thead><tr><th>Year</th><th>Total Tasks</th><th>Completed</th><th>In Progress</th><th>Overdue</th><th>Blocked</th><th>Completion %</th></tr></thead><tbody id="yearlyTableBody"></tbody></table></div></div></div>
                    <div class="card" style="margin-top:24px;"><div class="card-header"><h3><i class="fas fa-chart-line"></i> Yearly Comparison</h3></div><div class="card-body"><div class="chart-container"><canvas id="yearlyChart"></canvas></div></div></div>
                </div>

                <!-- ==================== PERFORMANCE ==================== -->
                <div class="page-section" id="page-performance">
                    <div class="section-header"><div class="section-title"><i class="fas fa-chart-pie"></i> Performance Visualization</div></div>
                    <div class="charts-grid">
                        <div class="card"><div class="card-header"><h3><i class="fas fa-chart-pie"></i> Status Distribution</h3></div><div class="card-body"><div class="chart-container"><canvas id="perfStatusChart"></canvas></div></div></div>
                        <div class="card"><div class="card-header"><h3><i class="fas fa-chart-doughnut"></i> Priority Distribution</h3></div><div class="card-body"><div class="chart-container"><canvas id="perfPriorityChart"></canvas></div></div></div>
                    </div>
                    <div class="card" style="margin-top:24px;"><div class="card-header"><h3><i class="fas fa-chart-line"></i> Monthly Task Creation vs Completion</h3></div><div class="card-body"><div class="chart-container"><canvas id="perfTrendChart"></canvas></div></div></div>
                </div>

                <!-- ==================== PROJECTS ==================== -->
                <div class="page-section" id="page-projects">
                    <div class="section-header">
                        <div class="section-title"><i class="fas fa-project-diagram"></i> Project Health Dashboard</div>
                        <div class="section-actions">
                            <button class="btn btn-primary" onclick="openAddProjectModal()"><i class="fas fa-plus"></i> Add Project</button>
                        </div>
                    </div>
                    <div class="filter-bar">
                        <select id="filterProjectHealth" onchange="filterProjects()"><option value="">All Health Status</option><option value="healthy">Healthy</option><option value="watch">Watch</option><option value="risk">At Risk</option></select>
                        <select id="filterProjectCompletion" onchange="filterProjects()"><option value="">All Completion %</option><option value="0-25">0-25%</option><option value="25-50">25-50%</option><option value="50-75">50-75%</option><option value="75-100">75-100%</option></select>
                    </div>
                    <div class="card"><div class="card-header"><h3><i class="fas fa-table"></i> Project Summary</h3></div><div class="card-body" style="padding:0;"><div style="overflow-x:auto;"><table class="data-table"><thead><tr><th>Project ID</th><th>Project Name</th><th>Total Tasks</th><th>Avg % Complete</th><th>Overdue Count</th><th>Overdue Days</th><th>Budget Risk</th><th>Health</th><th>Actions</th></tr></thead><tbody id="projectTableBody"></tbody></table></div></div></div>
                </div>

                <!-- ==================== RISK ==================== -->
                <div class="page-section" id="page-risk">
                    <div class="section-header"><div class="section-title"><i class="fas fa-bolt"></i> Risk Assessment Engine</div></div>
                    <div class="stats-grid" style="margin-bottom:20px;">
                        <div class="stat-card red"><div class="stat-header"><div class="stat-icon red"><i class="fas fa-skull-crossbones"></i></div></div><div class="stat-value" id="riskCritical">0</div><div class="stat-label">Critical Risk</div></div>
                        <div class="stat-card orange"><div class="stat-header"><div class="stat-icon orange"><i class="fas fa-exclamation"></i></div></div><div class="stat-value" id="riskHigh">0</div><div class="stat-label">High Risk</div></div>
                        <div class="stat-card cyan"><div class="stat-header"><div class="stat-icon cyan"><i class="fas fa-shield-alt"></i></div></div><div class="stat-value" id="riskMedium">0</div><div class="stat-label">Medium Risk</div></div>
                    </div>
                    <div class="card"><div class="card-header"><h3><i class="fas fa-list"></i> Risk Scored Tasks</h3></div><div class="card-body" style="padding:0;"><div style="overflow-x:auto;"><table class="data-table"><thead><tr><th>Task ID</th><th>Task</th><th>Owner</th><th>Priority</th><th>Risk Score</th><th>Risk Level</th><th>Days Overdue</th><th>Variance</th></tr></thead><tbody id="riskTableBody"></tbody></table></div></div></div>
                </div>

                <!-- ==================== EVM ==================== -->
                <div class="page-section" id="page-evm">
                    <div class="section-header"><div class="section-title"><i class="fas fa-dollar-sign"></i> Earned Value Management</div></div>
                    <div class="card"><div class="card-header"><h3><i class="fas fa-table"></i> EVM Financials by Project</h3></div><div class="card-body" style="padding:0;"><div style="overflow-x:auto;"><table class="data-table"><thead><tr><th>Project</th><th>BCWS</th><th>BCWP</th><th>SV</th><th>CV</th><th>SPI</th><th>CPI</th><th>EAC</th></tr></thead><tbody id="evmTableBody"></tbody></table></div></div></div>
                </div>

                <!-- ==================== LEADS (FULL CRM) ==================== -->
                <div class="page-section" id="page-leads">
                    <div class="section-header">
                        <div class="section-title"><i class="fas fa-address-book"></i> Lead & CRM Tracker</div>
                        <div class="section-actions">
                            <button class="btn btn-primary" onclick="openAddLeadModal()"><i class="fas fa-plus"></i> Add Lead</button>
                            <button class="btn btn-outline" onclick="showPage('pipeline')"><i class="fas fa-filter"></i> Pipeline View</button>
                        </div>
                    </div>
                    <div class="filter-bar">
                        <select id="filterLeadStage" onchange="filterLeads()"><option value="">All Stages</option><option value="New Lead">New Lead</option><option value="Contacted">Contacted</option><option value="Interested">Interested</option><option value="Proposal Sent">Proposal Sent</option><option value="Negotiation">Negotiation</option><option value="Follow-up">Follow-up</option><option value="Won">Won</option><option value="Lost">Lost</option><option value="Dormant">Dormant</option></select>
                        <select id="filterLeadAssigned" onchange="filterLeads()"><option value="">All Assigned</option></select>
                        <select id="filterLeadLastContact" onchange="filterLeads()"><option value="">Last Contact</option><option value="7">Last 7 days</option><option value="14">Last 14 days</option><option value="30">Last 30 days</option><option value="90">Last 90 days</option></select>
                        <input type="text" id="leadSearch" placeholder="Search leads..." onkeyup="filterLeads()" style="width:200px;">
                    </div>
                    <div class="card"><div class="card-body" style="padding:0;"><div style="overflow-x:auto;"><table class="data-table"><thead><tr><th>Lead ID</th><th>Lead Name</th><th>Contact</th><th>Company</th><th>Stage</th><th>Value</th><th>Last Contact</th><th>Follow-up</th><th>Assigned To</th><th>Actions</th></tr></thead><tbody id="leadsTableBody"></tbody></table></div></div></div>
                </div>

                <!-- ==================== PIPELINE ==================== -->
                <div class="page-section" id="page-pipeline">
                    <div class="section-header">
                        <div class="section-title"><i class="fas fa-filter"></i> Sales Pipeline</div>
                        <div class="section-actions">
                            <button class="btn btn-primary" onclick="openAddLeadModal()"><i class="fas fa-plus"></i> Add Lead</button>
                        </div>
                    </div>
                    <div class="stats-grid" style="margin-bottom:20px;">
                        <div class="stat-card blue"><div class="stat-header"><div class="stat-icon blue"><i class="fas fa-funnel-dollar"></i></div></div><div class="stat-value" id="pipelineTotalValue">0</div><div class="stat-label">Pipeline Value</div></div>
                        <div class="stat-card green"><div class="stat-header"><div class="stat-icon green"><i class="fas fa-handshake"></i></div></div><div class="stat-value" id="pipelineWonValue">0</div><div class="stat-label">Won Value</div></div>
                        <div class="stat-card orange"><div class="stat-header"><div class="stat-icon orange"><i class="fas fa-percentage"></i></div></div><div class="stat-value" id="pipelineConversion">0%</div><div class="stat-label">Conversion Rate</div></div>
                    </div>
                    <div id="pipelineContainer" class="pipeline-container"></div>
                </div>

                <!-- ==================== ACTIVITIES ==================== -->
                <div class="page-section" id="page-activities">
                    <div class="section-header">
                        <div class="section-title"><i class="fas fa-history"></i> Activity Timeline</div>
                        <div class="section-actions">
                            <button class="btn btn-primary" onclick="openAddActivityModal()"><i class="fas fa-plus"></i> Log Activity</button>
                        </div>
                    </div>
                    <div class="filter-bar">
                        <select id="filterActivityType" onchange="filterActivities()"><option value="">All Types</option><option value="Call">Call</option><option value="Email">Email</option><option value="Meeting">Meeting</option><option value="Note">Note</option><option value="Task">Task</option></select>
                        <select id="filterActivityEntity" onchange="filterActivities()"><option value="">All Entities</option><option value="lead">Leads</option><option value="project">Projects</option><option value="task">Tasks</option></select>
                    </div>
                    <div class="card"><div class="card-body"><div id="activitiesTimeline"></div></div></div>
                </div>

                <!-- ==================== GOALS ==================== -->
                <div class="page-section" id="page-goals">
                    <div class="section-header">
                        <div class="section-title"><i class="fas fa-bullseye"></i> Daily Goal Tracker</div>
                        <div class="section-actions">
                            <button class="btn btn-primary" onclick="openAddGoalModal()"><i class="fas fa-plus"></i> New Goal</button>
                        </div>
                    </div>
                    <div class="stats-grid" style="margin-bottom:20px;">
                        <div class="stat-card blue"><div class="stat-header"><div class="stat-icon blue"><i class="fas fa-bullseye"></i></div></div><div class="stat-value" id="goalsTotal">0</div><div class="stat-label">Total Goals</div></div>
                        <div class="stat-card green"><div class="stat-header"><div class="stat-icon green"><i class="fas fa-check-circle"></i></div></div><div class="stat-value" id="goalsCompleted">0</div><div class="stat-label">Completed</div></div>
                        <div class="stat-card orange"><div class="stat-header"><div class="stat-icon orange"><i class="fas fa-percentage"></i></div></div><div class="stat-value" id="goalsAvgProgress">0%</div><div class="stat-label">Avg Progress</div></div>
                    </div>
                    <div id="goalsContainer"></div>
                </div>

                <!-- ==================== CALENDAR ==================== -->
                <div class="page-section" id="page-calendar">
                    <div class="section-header">
                        <div class="section-title"><i class="fas fa-calendar"></i> Calendar View</div>
                        <div class="section-actions">
                            <button class="btn btn-outline" onclick="changeMonth(-1)"><i class="fas fa-chevron-left"></i></button>
                            <span id="calendarMonthYear" style="font-weight:700;font-size:16px;min-width:150px;text-align:center;"></span>
                            <button class="btn btn-outline" onclick="changeMonth(1)"><i class="fas fa-chevron-right"></i></button>
                            <button class="btn btn-primary" onclick="goToToday()" style="margin-left:10px;">Today</button>
                        </div>
                    </div>
                    <div class="card"><div class="card-body"><div id="calendarContainer" class="calendar-grid"></div></div></div>
                    <div class="card" style="margin-top:20px;">
                        <div class="card-header"><h3><i class="fas fa-list"></i> Events for Selected Date</h3></div>
                        <div class="card-body"><div id="calendarEventList"></div></div>
                    </div>
                </div>

                <!-- ==================== IMPORT/EXPORT ==================== -->
                <div class="page-section" id="page-importexport">
                    <div class="section-header"><div class="section-title"><i class="fas fa-file-import"></i> Import / Export Data</div></div>
                    <div class="stats-grid">
                        <div class="card">
                            <div class="card-header"><h3><i class="fas fa-file-csv"></i> Import from Excel/CSV</h3></div>
                            <div class="card-body">
                                <p style="margin-bottom:16px;color:var(--text-secondary);">Upload your Excel or CSV file to import tasks, leads, or projects.</p>
                                <div class="file-actions">
                                    <div class="file-input-wrapper">
                                        <button class="btn btn-primary"><i class="fas fa-upload"></i> Choose File</button>
                                        <input type="file" id="importFile" accept=".csv,.xlsx,.xls" onchange="handleImport(event)">
                                    </div>
                                    <select id="importType" style="padding:10px 16px;border-radius:10px;border:1px solid var(--border-color);">
                                        <option value="tasks">Tasks</option>
                                        <option value="leads">Leads</option>
                                        <option value="projects">Projects</option>
                                    </select>
                                </div>
                                <p id="importStatus" style="margin-top:12px;font-size:13px;"></p>
                            </div>
                        </div>
                        <div class="card">
                            <div class="card-header"><h3><i class="fas fa-download"></i> Export Data</h3></div>
                            <div class="card-body">
                                <p style="margin-bottom:16px;color:var(--text-secondary);">Download your data in various formats.</p>
                                <div class="file-actions" style="flex-wrap:wrap;gap:8px;">
                                    <button class="btn btn-outline" onclick="exportToCSV('tasks')"><i class="fas fa-file-csv"></i> Export Tasks CSV</button>
                                    <button class="btn btn-outline" onclick="exportToCSV('leads')"><i class="fas fa-file-csv"></i> Export Leads CSV</button>
                                    <button class="btn btn-outline" onclick="exportToJSON()"><i class="fas fa-file-code"></i> Export Full JSON</button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="card" style="margin-top:24px;">
                        <div class="card-header"><h3><i class="fas fa-database"></i> Data Summary</h3></div>
                        <div class="card-body">
                            <div class="stats-grid" style="margin-bottom:0;">
                                <div class="stat-card blue"><div class="stat-value" id="expTotalTasks">0</div><div class="stat-label">Tasks</div></div>
                                <div class="stat-card green"><div class="stat-value" id="expTotalOwners">0</div><div class="stat-label">Owners</div></div>
                                <div class="stat-card orange"><div class="stat-value" id="expTotalProjects">0</div><div class="stat-label">Projects</div></div>
                                <div class="stat-card purple"><div class="stat-value" id="expTotalLeads">0</div><div class="stat-label">Leads</div></div>
                                <div class="stat-card cyan"><div class="stat-value" id="expTotalGoals">0</div><div class="stat-label">Goals</div></div>
                                <div class="stat-card red"><div class="stat-value" id="expTotalActivities">0</div><div class="stat-label">Activities</div></div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </main>
    </div>

    <!-- ==================== MODALS ==================== -->

    <!-- Add/Edit Task Modal -->
    <div class="modal-overlay" id="taskModal">
        <div class="modal">
            <div class="modal-header"><h3 id="taskModalTitle"><i class="fas fa-plus-circle"></i> Add New Task</h3><button class="modal-close" onclick="closeModal('taskModal')">&times;</button></div>
            <div class="modal-body">
                <input type="hidden" id="taskEditId">
                <div class="form-row">
                    <div class="form-group"><label>Task ID <small class="text-muted">(Auto-generated)</small></label><input type="text" id="taskId" readonly style="background:var(--bg-body);color:var(--text-secondary);"></div>
                    <div class="form-group"><label>Project</label><input type="text" id="taskProject" placeholder="Project name"></div>
                </div>
                <div class="form-group"><label>Task Name *</label><input type="text" id="taskName" placeholder="Enter task name"></div>
                <div class="form-row">
                    <div class="form-group"><label>Owner</label><input type="text" id="taskOwner" placeholder="Assign to"></div>
                    <div class="form-group"><label>Priority</label><select id="taskPriority"><option value="Low">Low</option><option value="Medium">Medium</option><option value="High" selected>High</option><option value="Critical">Critical</option></select></div>
                </div>
                <div class="form-row">
                    <div class="form-group"><label>Status</label><select id="taskStatus"><option value="Not Started">Not Started</option><option value="In Progress" selected>In Progress</option><option value="Done">Done</option><option value="Blocked">Blocked</option></select></div>
                    <div class="form-group"><label>% Complete</label><input type="number" id="taskPercent" min="0" max="100" value="0"></div>
                </div>
                <div class="form-row">
                    <div class="form-group"><label>Start Date</label><input type="date" id="taskStart"></div>
                    <div class="form-group"><label>Due Date</label><input type="date" id="taskDue"></div>
                </div>
                <div class="form-group">
                    <label>Depends On <small class="text-muted">(Task ID this task depends on)</small></label>
                    <input type="text" id="taskDependsOn" placeholder="e.g., TASK-2026-0001">
                </div>
                <div class="form-group">
                    <label>Files</label>
                    <div class="file-dropzone" onclick="document.getElementById('taskFileInput').click()">
                        <i class="fas fa-cloud-upload-alt"></i>
                        <p>Click to upload files</p>
                        <p class="fs-sm text-muted">PDF, DOC, XLS, Images</p>
                    </div>
                    <input type="file" id="taskFileInput" multiple style="display:none;" onchange="handleFileUpload(event, 'task')">
                    <div class="file-list" id="taskFileList"></div>
                </div>
                <div class="form-group">
                    <label>Activity Timeline</label>
                    <div id="taskActivityPreview" style="max-height:200px;overflow-y:auto;"></div>
                </div>
                <div class="form-actions" style="display:flex;gap:10px;">
                    <button class="btn btn-primary" style="flex:1;" onclick="saveTask()"><i class="fas fa-save"></i> Save Task</button>
                    <button class="btn btn-outline" onclick="openAddActivityModal('task', document.getElementById('taskEditId').value)"><i class="fas fa-plus"></i> Add Activity</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Add/Edit Lead Modal -->
    <div class="modal-overlay" id="leadModal">
        <div class="modal modal-lg">
            <div class="modal-header"><h3 id="leadModalTitle"><i class="fas fa-plus-circle"></i> Add New Lead</h3><button class="modal-close" onclick="closeModal('leadModal')">&times;</button></div>
            <div class="modal-body">
                <input type="hidden" id="leadEditId">
                <div class="tabs">
                    <div class="tab active" onclick="switchTab('leadTabDetails')">Details</div>
                    <div class="tab" onclick="switchTab('leadTabActivities')">Activities</div>
                    <div class="tab" onclick="switchTab('leadTabFiles')">Files</div>
                </div>
                <div class="tab-content active" id="leadTabDetails">
                    <div class="form-row">
                        <div class="form-group"><label>Lead ID <small class="text-muted">(Auto-generated)</small></label><input type="text" id="leadId" readonly style="background:var(--bg-body);color:var(--text-secondary);"></div>
                        <div class="form-group"><label>Lead Name *</label><input type="text" id="leadName" placeholder="Enter lead name"></div>
                    </div>
                    <div class="form-row">
                        <div class="form-group"><label>Contact</label><input type="text" id="leadContact" placeholder="Phone or email"></div>
                        <div class="form-group"><label>Company</label><input type="text" id="leadCompany" placeholder="Company name"></div>
                    </div>
                    <div class="form-row">
                        <div class="form-group"><label>Stage</label><select id="leadStage"><option value="New Lead">New Lead</option><option value="Contacted">Contacted</option><option value="Interested">Interested</option><option value="Proposal Sent">Proposal Sent</option><option value="Negotiation">Negotiation</option><option value="Follow-up">Follow-up</option><option value="Won">Won</option><option value="Lost">Lost</option><option value="Dormant">Dormant</option></select></div>
                        <div class="form-group"><label>Value (KES)</label><input type="number" id="leadValue" placeholder="0" min="0"></div>
                    </div>
                    <div class="form-row">
                        <div class="form-group"><label>Last Contact Date</label><input type="date" id="leadLastContact"></div>
                        <div class="form-group"><label>Follow-up Date</label><input type="date" id="leadFollowUp"></div>
                    </div>
                    <div class="form-row">
                        <div class="form-group"><label>Assigned To</label><input type="text" id="leadAssigned" placeholder="Assign to"></div>
                        <div class="form-group"><label>Source</label><input type="text" id="leadSource" placeholder="Lead source"></div>
                    </div>
                    <div class="form-group"><label>Notes</label><textarea id="leadNotes" placeholder="Enter notes..."></textarea></div>
                </div>
                <div class="tab-content" id="leadTabActivities">
                    <div id="leadActivitiesPreview" style="max-height:300px;overflow-y:auto;"></div>
                    <button class="btn btn-primary" style="margin-top:12px;width:100%;" onclick="openAddActivityModal('lead', document.getElementById('leadEditId').value)"><i class="fas fa-plus"></i> Add Activity</button>
                </div>
                <div class="tab-content" id="leadTabFiles">
                    <div class="file-dropzone" onclick="document.getElementById('leadFileInput').click()">
                        <i class="fas fa-cloud-upload-alt"></i>
                        <p>Click to upload files</p>
                        <p class="fs-sm text-muted">Contracts, Quotations, PDFs</p>
                    </div>
                    <input type="file" id="leadFileInput" multiple style="display:none;" onchange="handleFileUpload(event, 'lead')">
                    <div class="file-list" id="leadFileList"></div>
                </div>
                <div style="display:flex;gap:10px;margin-top:20px;">
                    <button class="btn btn-primary" style="flex:1;" onclick="saveLead()"><i class="fas fa-save"></i> Save Lead</button>
                    <button class="btn btn-success" id="convertLeadBtn" style="display:none;" onclick="convertLeadToProject()"><i class="fas fa-exchange-alt"></i> Convert to Project</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Add Activity Modal -->
    <div class="modal-overlay" id="activityModal">
        <div class="modal">
            <div class="modal-header"><h3><i class="fas fa-plus-circle"></i> Add Activity</h3><button class="modal-close" onclick="closeModal('activityModal')">&times;</button></div>
            <div class="modal-body">
                <input type="hidden" id="activityEntityType">
                <input type="hidden" id="activityEntityId">
                <div class="form-row">
                    <div class="form-group"><label>Type</label><select id="activityType"><option value="Call">Call</option><option value="Email">Email</option><option value="Meeting">Meeting</option><option value="Note">Note</option><option value="Task">Task</option></select></div>
                    <div class="form-group"><label>Status/Outcome</label><input type="text" id="activityStatus" placeholder="e.g., Interested, No answer"></div>
                </div>
                <div class="form-group"><label>Note</label><textarea id="activityNote" placeholder="Enter activity details..."></textarea></div>
                <button class="btn btn-primary" style="width:100%;" onclick="saveActivity()"><i class="fas fa-save"></i> Save Activity</button>
            </div>
        </div>
    </div>

    <!-- Add Project Modal -->
    <div class="modal-overlay" id="projectModal">
        <div class="modal">
            <div class="modal-header"><h3 id="projectModalTitle"><i class="fas fa-plus-circle"></i> Add New Project</h3><button class="modal-close" onclick="closeModal('projectModal')">&times;</button></div>
            <div class="modal-body">
                <input type="hidden" id="projectEditId">
                <div class="form-row">
                    <div class="form-group"><label>Project ID <small class="text-muted">(Auto-generated)</small></label><input type="text" id="projectId" readonly style="background:var(--bg-body);color:var(--text-secondary);"></div>
                    <div class="form-group"><label>Project Name *</label><input type="text" id="projectName" placeholder="Enter project name"></div>
                </div>
                <div class="form-row">
                    <div class="form-group"><label>Manager</label><input type="text" id="projectManager" placeholder="Project manager"></div>
                    <div class="form-group"><label>Budget (KES)</label><input type="number" id="projectBudget" placeholder="0" min="0"></div>
                </div>
                <div class="form-row">
                    <div class="form-group"><label>Start Date</label><input type="date" id="projectStart"></div>
                    <div class="form-group"><label>End Date</label><input type="date" id="projectEnd"></div>
                </div>
                <div class="form-group"><label>Description</label><textarea id="projectDesc" placeholder="Project description..."></textarea></div>
                <div class="form-group">
                    <label>Source Lead <small class="text-muted">(If converted from lead)</small></label>
                    <input type="text" id="projectSourceLead" readonly style="background:var(--bg-body);color:var(--text-secondary);">
                </div>
                <button class="btn btn-primary" style="width:100%;" onclick="saveProject()"><i class="fas fa-save"></i> Save Project</button>
            </div>
        </div>
    </div>

    <!-- Add Goal Modal -->
    <div class="modal-overlay" id="goalModal">
        <div class="modal">
            <div class="modal-header"><h3 id="goalModalTitle"><i class="fas fa-bullseye"></i> New Goal</h3><button class="modal-close" onclick="closeModal('goalModal')">&times;</button></div>
            <div class="modal-body">
                <input type="hidden" id="goalEditId">
                <div class="form-group"><label>Goal Title *</label><input type="text" id="goalTitle" placeholder="e.g., Read CIFA Notes"></div>
                <div class="form-row">
                    <div class="form-group"><label>Target Value *</label><input type="number" id="goalTarget" placeholder="300" min="1"></div>
                    <div class="form-group"><label>Unit</label><input type="text" id="goalUnit" placeholder="Pages, Hours, Tasks"></div>
                </div>
                <div class="form-row">
                    <div class="form-group"><label>Start Date</label><input type="date" id="goalStart"></div>
                    <div class="form-group"><label>End Date</label><input type="date" id="goalEnd"></div>
                </div>
                <div class="form-group"><label>Description</label><textarea id="goalDesc" placeholder="Goal description..."></textarea></div>
                <button class="btn btn-primary" style="width:100%;" onclick="saveGoal()"><i class="fas fa-save"></i> Save Goal</button>
            </div>
        </div>
    </div>

    <!-- Log Progress Modal -->
    <div class="modal-overlay" id="logProgressModal">
        <div class="modal">
            <div class="modal-header"><h3><i class="fas fa-chart-line"></i> Log Progress</h3><button class="modal-close" onclick="closeModal('logProgressModal')">&times;</button></div>
            <div class="modal-body">
                <input type="hidden" id="logGoalId">
                <div class="form-row">
                    <div class="form-group"><label>Date</label><input type="date" id="logDate"></div>
                    <div class="form-group"><label>Progress Amount</label><input type="number" id="logProgress" placeholder="e.g., 12" min="0"></div>
                </div>
                <div class="form-group"><label>Note</label><textarea id="logNote" placeholder="e.g., Finished Chapter 4"></textarea></div>
                <button class="btn btn-primary" style="width:100%;" onclick="saveLogProgress()"><i class="fas fa-save"></i> Log Progress</button>
            </div>
        </div>
    </div>

    <!-- View Lead/Project Detail Modal -->
    <div class="modal-overlay" id="detailModal">
        <div class="modal modal-lg">
            <div class="modal-header"><h3 id="detailModalTitle">Details</h3><button class="modal-close" onclick="closeModal('detailModal')">&times;</button></div>
            <div class="modal-body" id="detailModalBody"></div>
        </div>
    </div>

    <!-- Alerts Panel -->
    <div class="alerts-panel" id="alertsPanel">
        <div class="alerts-header">
            <h3><i class="fas fa-bell"></i> Smart Alerts</h3>
            <button class="header-btn" onclick="toggleAlertsPanel()" style="border:none;"><i class="fas fa-times"></i></button>
        </div>
        <div id="alertsList"></div>
    </div>

    <!-- Notification -->
    <div class="notification" id="notification"></div>

<script>
// ============================================================================
// ENTERPRISE TASK TRACKER - UPGRADED ERP PLATFORM
// ============================================================================
// Architecture: Modular, Storage-Abstracted, Future-Ready for Cloud DB
// ============================================================================

// ==================== CONSTANTS & CONFIG ====================
const CONFIG = {
    APP_NAME: 'Task-Majani',
    VERSION: '2.1.0',
    STORAGE_PREFIX: 'ett_v2_',
    DEMO_SEEDED_KEY: 'ett_v2_demo_seeded',
    DATE_FORMAT: 'YYYY-MM-DD',
    ID_FORMATS: {
        TASK: 'TASK',
        LEAD: 'LEAD',
        PROJECT: 'PROJ',
        CALL: 'CALL',
        ACTIVITY: 'ACT',
        GOAL: 'GOAL'
    }
};

const LEAD_STAGES = [
    "New Lead", "Contacted", "Interested", "Proposal Sent",
    "Negotiation", "Follow-up", "Won", "Lost", "Dormant"
];

const ROLES = {
    ADMIN: { name: 'Admin', permissions: ['full'], label: 'System Administrator' },
    MANAGER: { name: 'Manager', permissions: ['edit', 'view'], label: 'Project Manager' },
    STAFF: { name: 'Staff', permissions: ['limited'], label: 'Team Member' },
    VIEWER: { name: 'Viewer', permissions: ['read'], label: 'Read Only' }
};

const DEMO_USERS = {
    admin: { password: 'admin123', role: 'ADMIN', name: 'Admin' },
    manager: { password: 'manager123', role: 'MANAGER', name: 'Manager' },
    staff: { password: 'staff123', role: 'STAFF', name: 'Staff' },
    viewer: { password: 'viewer123', role: 'VIEWER', name: 'Viewer' }
};

// ==================== STORAGE ABSTRACTION LAYER ====================
// Dual-layer: localStorage (fast) + IndexedDB (persistent, larger)
// Future-ready: Can be swapped with Firebase/Supabase/PostgreSQL/MySQL

const IndexedDBStorage = {
    db: null,
    DB_NAME: 'TaskMajaniDB',
    DB_VERSION: 1,
    STORE_NAME: 'appData',

    async init() {
        return new Promise((resolve, reject) => {
            const request = indexedDB.open(this.DB_NAME, this.DB_VERSION);
            request.onupgradeneeded = (event) => {
                const db = event.target.result;
                if (!db.objectStoreNames.contains(this.STORE_NAME)) {
                    db.createObjectStore(this.STORE_NAME);
                }
            };
            request.onsuccess = (event) => {
                this.db = event.target.result;
                resolve(this.db);
            };
            request.onerror = (event) => {
                console.error('IndexedDB init error:', event.target.error);
                reject(event.target.error);
            };
        });
    },

    async save(key, data) {
        if (!this.db) await this.init();
        return new Promise((resolve, reject) => {
            const tx = this.db.transaction([this.STORE_NAME], 'readwrite');
            const store = tx.objectStore(this.STORE_NAME);
            const request = store.put(JSON.stringify(data), CONFIG.STORAGE_PREFIX + key);
            request.onsuccess = () => resolve(true);
            request.onerror = () => reject(request.error);
        });
    },

    async load(key, fallback = []) {
        if (!this.db) await this.init();
        return new Promise((resolve) => {
            const tx = this.db.transaction([this.STORE_NAME], 'readonly');
            const store = tx.objectStore(this.STORE_NAME);
            const request = store.get(CONFIG.STORAGE_PREFIX + key);
            request.onsuccess = () => {
                try {
                    resolve(request.result ? JSON.parse(request.result) : fallback);
                } catch (e) {
                    resolve(fallback);
                }
            };
            request.onerror = () => resolve(fallback);
        });
    }
};

const Storage = {
    async save(key, data) {
        // Always save to localStorage (fast)
        try {
            localStorage.setItem(CONFIG.STORAGE_PREFIX + key, JSON.stringify(data));
        } catch (e) {
            console.warn('localStorage save failed, using IndexedDB:', e);
        }
        // Also save to IndexedDB (persistent, larger capacity)
        try {
            await IndexedDBStorage.save(key, data);
        } catch (e) {
            console.error('IndexedDB save error:', e);
        }
        return true;
    },

    load(key, fallback = []) {
        // Try localStorage first (fastest)
        try {
            const item = localStorage.getItem(CONFIG.STORAGE_PREFIX + key);
            if (item) {
                const parsed = JSON.parse(item);
                // Also sync to IndexedDB for redundancy
                IndexedDBStorage.save(key, parsed).catch(() => {});
                return parsed;
            }
        } catch (e) {
            console.warn('localStorage load failed, trying IndexedDB:', e);
        }
        // Fallback to IndexedDB
        try {
            return IndexedDBStorage.load(key, fallback);
        } catch (e) {
            console.error('IndexedDB load error:', e);
            return fallback;
        }
    },

    remove(key) {
        localStorage.removeItem(CONFIG.STORAGE_PREFIX + key);
        IndexedDBStorage.init().then(() => {
            const tx = IndexedDBStorage.db.transaction([IndexedDBStorage.STORE_NAME], 'readwrite');
            tx.objectStore(IndexedDBStorage.STORE_NAME).delete(CONFIG.STORAGE_PREFIX + key);
        }).catch(() => {});
    },

    clear() {
        Object.keys(localStorage).forEach(k => {
            if (k.startsWith(CONFIG.STORAGE_PREFIX)) localStorage.removeItem(k);
        });
        IndexedDBStorage.init().then(() => {
            const tx = IndexedDBStorage.db.transaction([IndexedDBStorage.STORE_NAME], 'readwrite');
            tx.objectStore(IndexedDBStorage.STORE_NAME).clear();
        }).catch(() => {});
    },

    batchSave(operations) {
        operations.forEach(({ key, data }) => this.save(key, data));
    },

    // Storage diagnostics
    getStorageInfo() {
        let localTotal = 0;
        const breakdown = {};
        Object.keys(localStorage).forEach(key => {
            if (key.startsWith(CONFIG.STORAGE_PREFIX)) {
                const size = localStorage[key].length * 2;
                localTotal += size;
                breakdown[key.replace(CONFIG.STORAGE_PREFIX, '')] = (size / 1024).toFixed(2) + ' KB';
            }
        });
        return {
            localStorage: {
                total: (localTotal / 1024).toFixed(2) + ' KB',
                breakdown: breakdown
            }
        };
    }
};

// ==================== ID GENERATION ENGINE ====================
const IDGenerator = {
    generate(prefix, items) {
        const year = new Date().getFullYear();
        let max = 0;
        items.forEach(item => {
            if (!item.id) return;
            const parts = item.id.split('-');
            if (parts.length === 3 && parts[0] === prefix && parseInt(parts[1]) === year) {
                const num = parseInt(parts[2]);
                if (!isNaN(num) && num > max) max = num;
            }
        });
        return `${prefix}-${year}-${String(max + 1).padStart(4, '0')}`;
    },
    generateTask(items) { return this.generate(CONFIG.ID_FORMATS.TASK, items); },
    generateLead(items) { return this.generate(CONFIG.ID_FORMATS.LEAD, items); },
    generateProject(items) { return this.generate(CONFIG.ID_FORMATS.PROJECT, items); },
    generateCall(items) { return this.generate(CONFIG.ID_FORMATS.CALL, items); },
    generateActivity(items) { return this.generate(CONFIG.ID_FORMATS.ACTIVITY, items); },
    generateGoal(items) { return this.generate(CONFIG.ID_FORMATS.GOAL, items); }
};

// ==================== APP STATE ====================
const AppState = {
    tasks: [],
    leads: [],
    projects: [],
    activities: [],
    goals: [],
    files: [],
    settings: {},
    currentUser: null,
    currentRole: null,
    calendarDate: new Date(),
    charts: {},
    initialized: false
};

// ==================== DATA MIGRATION & INITIALIZATION ====================

// ==================== AUTO BACKUP ====================
function autoExportBackup() {
    try {
        const backup = {
            tasks: AppState.tasks,
            leads: AppState.leads,
            projects: AppState.projects,
            activities: AppState.activities,
            goals: AppState.goals,
            exported: new Date().toISOString(),
            version: CONFIG.VERSION,
            app: CONFIG.APP_NAME
        };
        const blob = new Blob([JSON.stringify(backup, null, 2)], { type: 'application/json' });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = 'task-majani-backup-' + new Date().toISOString().split('T')[0] + '.json';
        // Don't actually trigger download on beforeunload - just log
        URL.revokeObjectURL(url);
    } catch (e) {
        console.error('Auto backup failed:', e);
    }
}

function showStorageDiagnostics() {
    const info = Storage.getStorageInfo();
    let html = '<div style="padding:20px;">';
    html += '<h4 style="margin-bottom:12px;">📊 Storage Diagnostics</h4>';
    html += '<p><strong>localStorage Total:</strong> ' + info.localStorage.total + '</p>';
    html += '<p style="margin-top:12px;"><strong>Breakdown:</strong></p><ul style="margin-left:20px;">';
    for (const [key, size] of Object.entries(info.localStorage.breakdown)) {
        html += '<li>' + key + ': ' + size + '</li>';
    }
    html += '</ul>';
    html += '<p style="margin-top:16px;color:var(--primary);font-weight:600;">';
    html += '💡 Tip: Data is saved to both localStorage AND IndexedDB for redundancy.</p>';
    html += '</div>';

    const modal = document.createElement('div');
    modal.className = 'modal-overlay active';
    modal.innerHTML = '<div class="modal"><div class="modal-header"><h3>Storage Diagnostics</h3><button class="modal-close" onclick="this.closest('.modal-overlay').remove()">&times;</button></div><div class="modal-body">' + html + '</div></div>';
    document.body.appendChild(modal);
}

function init() {
    if (AppState.initialized) return;

    // Load all data from storage
    AppState.tasks = Storage.load('tasks', []);
    AppState.leads = Storage.load('leads', []);
    AppState.projects = Storage.load('projects', []);
    AppState.activities = Storage.load('activities', []);
    AppState.goals = Storage.load('goals', []);
    AppState.files = Storage.load('files', []);
    AppState.settings = Storage.load('settings', { theme: 'light', notifications: true });

    // Migrate old data if exists (from v1) - only if no v2 data exists
    if (AppState.tasks.length === 0 && AppState.leads.length === 0) {
        migrateOldData();
    }

    // Seed demo data ONLY if completely empty AND never seeded before
    const demoSeeded = localStorage.getItem(CONFIG.DEMO_SEEDED_KEY);
    if (AppState.tasks.length === 0 && AppState.leads.length === 0 && !demoSeeded) {
        seedDemoData();
        localStorage.setItem(CONFIG.DEMO_SEEDED_KEY, 'true');
    }

    // Apply theme
    applyTheme(AppState.settings.theme || 'light');

    // Check login
    const savedUser = Storage.load('currentUser', null);
    if (savedUser) {
        AppState.currentUser = savedUser;
        AppState.currentRole = savedUser.role;
        showApp();
    }

    // Set default dates in modals
    setDefaultDates();

    // Populate filters
    populateFilters();

    // Initial render
    renderAll();

    // Generate alerts
    generateAlerts();

    // Show storage info
    console.log('Task-Majani Storage Info:', Storage.getStorageInfo());

    AppState.initialized = true;

    // Keyboard shortcuts
    document.addEventListener('keydown', handleKeyboard);

    // Warn before leaving if data might be unsaved
    window.addEventListener('beforeunload', function(e) {
        // Auto-export backup on close
        autoExportBackup();
    });
}

function migrateOldData() {
    // Check for old format data (v1)
    const oldData = localStorage.getItem('ett_data');
    if (oldData && AppState.tasks.length === 0) {
        try {
            const parsed = JSON.parse(oldData);
            if (parsed.tasks) {
                AppState.tasks = parsed.tasks.map(t => ({
                    id: t['Task ID'] || IDGenerator.generateTask([]),
                    name: t['Task Name'] || '',
                    project: t.Project || '',
                    owner: t.Owner || '',
                    priority: t.Priority || 'Medium',
                    status: t.Status || 'Not Started',
                    percentComplete: t['% Complete'] || 0,
                    startDate: t['Start Date'] || '',
                    dueDate: t['Due Date'] || '',
                    daysOverdue: t['Days Overdue'] || 0,
                    signal: t.Signal || '',
                    month: t.Month || '',
                    year: t.Year || new Date().getFullYear(),
                    createdDate: t['Created Date'] || new Date().toISOString().split('T')[0],
                    activities: [],
                    dependsOn: null,
                    files: []
                }));
            }
            if (parsed.calls) {
                AppState.leads = parsed.calls.map(c => ({
                    id: IDGenerator.generateLead([]),
                    name: c['Lead Name'] || '',
                    contact: c.Contact || '',
                    company: '',
                    stage: 'New Lead',
                    value: 0,
                    lastContactDate: c['Last Call Date'] || '',
                    followUpDate: c['Follow-up Date'] || '',
                    assignedTo: '',
                    source: '',
                    notes: c.Notes || '',
                    createdDate: new Date().toISOString().split('T')[0],
                    activities: [],
                    files: [],
                    archived: false,
                    converted: false,
                    convertedProjectId: null
                }));
            }
            // Save migrated data
            saveAll();
            // Clear old format
            localStorage.removeItem('ett_data');
            localStorage.removeItem('ett_user');
            showNotification('Data migrated to new format successfully!', 'success');
        } catch (e) {
            console.error('Migration error:', e);
        }
    }
}

function seedDemoData() {
    // Seed tasks from original data
    const demoTasks = [
        { name: "Title Audit for all cretum Projects", project: "Audit", owner: "Joan", priority: "High", status: "In Progress", percentComplete: 0, dueDate: "2026-05-15", daysOverdue: 12 },
        { name: "Metrics", project: "Metrics for Debtors,IT,Digital Marketing,Registry", owner: "Departments", priority: "Medium", status: "In Progress", percentComplete: 0, dueDate: "2026-05-28", daysOverdue: 0 },
        { name: "Why lie to myself", project: "Novel", owner: "Majani", priority: "High", status: "In Progress", percentComplete: 50, dueDate: "2026-06-06", daysOverdue: 0 },
        { name: "Bima Emails", project: "Secretary 2-3", owner: "Majani", priority: "High", status: "Not Started", percentComplete: 0, dueDate: "2026-05-30", daysOverdue: 0 },
        { name: "Kisaju Titles", project: "Titles", owner: "Sinclair", priority: "High", status: "In Progress", percentComplete: 70, dueDate: "2026-05-30", daysOverdue: 0 },
        { name: "Metrics (Debtors,IT,Digital marketing,Registry)", project: "Manager", owner: "Majani", priority: "High", status: "Done", percentComplete: 100, dueDate: "2026-04-30", daysOverdue: 0 },
        { name: "Nanyuki Bus Tour", project: "Bus Tour", owner: "Team leaders", priority: "High", status: "Not Started", percentComplete: 0, dueDate: "2026-04-22", daysOverdue: 35 },
        { name: "Finlem Sacco", project: "Sales", owner: "Majani", priority: "High", status: "In Progress", percentComplete: 80, dueDate: "2026-05-30", daysOverdue: 0 },
        { name: "Isinya (encourage building)", project: "Sales", owner: "Team leaders", priority: "High", status: "Not Started", percentComplete: 20, dueDate: "2026-05-30", daysOverdue: 0 },
        { name: "Kinanie plots", project: "Sales", owner: "Team leaders", priority: "High", status: "In Progress", percentComplete: 30, dueDate: "2026-05-30", daysOverdue: 0 },
        { name: "Mutation (Machakos,kajiado,Nanyuki)", project: "Titles", owner: "Joan", priority: "High", status: "In Progress", percentComplete: 50, dueDate: "2026-07-06", daysOverdue: 0 },
        { name: "Microphone", project: "Content creation", owner: "IT", priority: "High", status: "In Progress", percentComplete: 30, dueDate: "2026-02-28", daysOverdue: 88 },
        { name: "Fortune Joska", project: "Titles", owner: "Joan", priority: "High", status: "Done", percentComplete: 100, dueDate: "2026-02-13", daysOverdue: 0 },
        { name: "Letter of credit, Fishbox", project: "Two-Three Sacco", owner: "Majani", priority: "High", status: "In Progress", percentComplete: 0, dueDate: "2026-06-19", daysOverdue: 0 },
        { name: "Japanes,Chines Embassy,Huawei", project: "Jihasib", owner: "Majani", priority: "Medium", status: "In Progress", percentComplete: 40, dueDate: "2026-06-19", daysOverdue: 0 },
        { name: "Kahawa Barrack coming from congo", project: "Sales", owner: "Sinclair", priority: "High", status: "In Progress", percentComplete: 30, dueDate: "2026-03-18", daysOverdue: 68 },
        { name: "Sign post and pin to share with clients", project: "Project description", owner: "IT/Field", priority: "High", status: "In Progress", percentComplete: 30, dueDate: "2026-04-19", daysOverdue: 37 },
        { name: "Now your neighbour", project: "Now your neighbour", owner: "Achieng", priority: "High", status: "In Progress", percentComplete: 70, dueDate: "2026-05-22", daysOverdue: 4 },
        { name: "Dera cooperative bank", project: "Sales", owner: "Majani", priority: "High", status: "In Progress", percentComplete: 50, dueDate: "2026-05-30", daysOverdue: 0 },
        { name: "Mugera Gulf bank", project: "Sales", owner: "Majani", priority: "High", status: "In Progress", percentComplete: 50, dueDate: "2026-05-30", daysOverdue: 0 },
        { name: "Kiota", project: "Sales", owner: "Majani", priority: "Medium", status: "In Progress", percentComplete: 60, dueDate: "2026-05-30", daysOverdue: 0 },
        { name: "Checklist for title and land", project: "Checklist for title and land", owner: "Field", priority: "High", status: "In Progress", percentComplete: 50, dueDate: "2026-05-30", daysOverdue: 0 },
        { name: "Cooperative follow up and DTB", project: "Sales/Juja", owner: "Majani", priority: "High", status: "In Progress", percentComplete: 50, dueDate: "2026-05-30", daysOverdue: 0 },
        { name: "Cretum Policies to the system", project: "Policies", owner: "Hudson", priority: "High", status: "Done", percentComplete: 100, dueDate: "2026-04-29", daysOverdue: 0 },
        { name: "Training Notes", project: "Training Swiss Berlin", owner: "Sinclair", priority: "Medium", status: "In Progress", percentComplete: 30, dueDate: "2026-05-30", daysOverdue: 0 },
        { name: "KRA returns", project: "Auditor", owner: "Eric", priority: "High", status: "In Progress", percentComplete: 50, dueDate: "2026-05-30", daysOverdue: 0 },
        { name: "Cheque commission", project: "MAM", owner: "Majani", priority: "High", status: "In Progress", percentComplete: 70, dueDate: "2026-05-30", daysOverdue: 0 },
        { name: "Copies of all Mutation", project: "Mutation", owner: "Joan", priority: "High", status: "In Progress", percentComplete: 20, dueDate: "2026-05-30", daysOverdue: 0 },
        { name: "Nyambura KCB bus tour", project: "Bus Tour", owner: "Majani", priority: "High", status: "Done", percentComplete: 100, dueDate: "2026-05-05", daysOverdue: 0 },
        { name: "Juja agents", project: "Agents", owner: "Majani", priority: "High", status: "In Progress", percentComplete: 50, dueDate: "2026-06-06", daysOverdue: 0 },
        { name: "National Bank", project: "Sales", owner: "Majani", priority: "High", status: "In Progress", percentComplete: 70, dueDate: "2025-05-19", daysOverdue: 355 },
        { name: "Kwal", project: "Sales", owner: "Eric", priority: "Medium", status: "In Progress", percentComplete: 30, dueDate: "2026-05-30", daysOverdue: 0 },
        { name: "Acacia 1 RIM", project: "RIM", owner: "Joan", priority: "High", status: "In Progress", percentComplete: 50, dueDate: "2026-05-30", daysOverdue: 0 },
        { name: "Data protection", project: "Data protection", owner: "Hudson", priority: "High", status: "In Progress", percentComplete: 70, dueDate: "2026-05-30", daysOverdue: 0 },
        { name: "PAF", project: "PAF", owner: "Kajano", priority: "High", status: "In Progress", percentComplete: 70, dueDate: "2026-05-05", daysOverdue: 27 },
        { name: "Hudson system", project: "BLV", owner: "Hudson", priority: "High", status: "In Progress", percentComplete: 50, dueDate: "2026-06-08", daysOverdue: 0 },
        { name: "Tc accounting system", project: "TC", owner: "Majani", priority: "High", status: "Done", percentComplete: 100, dueDate: "2026-05-22", daysOverdue: 0 },
        { name: "Filling accounts cretum", project: "KRA", owner: "Eric", priority: "Critical", status: "In Progress", percentComplete: 90, dueDate: "2026-05-30", daysOverdue: 0 },
        { name: "Kileleshwa project", project: "Sales", owner: "Majani", priority: "Critical", status: "In Progress", percentComplete: 50, dueDate: "2026-05-30", daysOverdue: 0 }
    ];

    demoTasks.forEach((t, i) => {
        AppState.tasks.push({
            id: IDGenerator.generateTask(AppState.tasks),
            name: t.name,
            project: t.project,
            owner: t.owner,
            priority: t.priority,
            status: t.status,
            percentComplete: t.percentComplete,
            startDate: '',
            dueDate: t.dueDate,
            daysOverdue: t.daysOverdue,
            signal: t.daysOverdue > 0 ? '🚨 OVERDUE' : t.percentComplete >= t.daysOverdue ? '📈 AHEAD' : '📉 SLIPPAGE',
            month: ['Jan','Feb','Mar','Apr','May'][Math.floor(Math.random() * 5)],
            year: 2026,
            createdDate: '2026-05-26',
            activities: [],
            dependsOn: null,
            files: []
        });
    });

    // Seed leads
    const demoLeads = [
        { name: "Lilian Waithera", contact: "", company: "", stage: "Contacted", value: 0, lastContact: "2026-01-30", followUp: "2026-05-30" },
        { name: "Raphael Nyange", contact: "", company: "", stage: "Interested", value: 150000, lastContact: "2026-01-31", followUp: "2026-05-31" },
        { name: "Joyce", contact: "", company: "", stage: "New Lead", value: 0, lastContact: "2026-02-25", followUp: "2026-06-01" },
        { name: "Hannah", contact: "", company: "", stage: "Proposal Sent", value: 250000, lastContact: "2026-02-25", followUp: "2026-06-02" },
        { name: "Mary", contact: "", company: "", stage: "Negotiation", value: 500000, lastContact: "2026-02-25", followUp: "2026-06-03" },
        { name: "Recheal gichea", contact: "", company: "", stage: "Follow-up", value: 100000, lastContact: "2026-02-26", followUp: "2026-06-04" },
        { name: "Catherine", contact: "", company: "", stage: "Contacted", value: 0, lastContact: "2026-04-20", followUp: "2026-06-05" },
        { name: "Rael", contact: "", company: "", stage: "Interested", value: 200000, lastContact: "2026-02-26", followUp: "2026-06-06" },
        { name: "Eric", contact: "", company: "", stage: "New Lead", value: 0, lastContact: "2026-02-27", followUp: "2026-06-07" },
        { name: "Mary Sister", contact: "", company: "", stage: "Dormant", value: 0, lastContact: "2026-04-25", followUp: "2026-06-08" },
        { name: "John Thuku", contact: "", company: "", stage: "Contacted", value: 0, lastContact: "2026-04-13", followUp: "2026-06-09" },
        { name: "Patrick Nation Media", contact: "", company: "Nation Media", stage: "Proposal Sent", value: 750000, lastContact: "2026-04-15", followUp: "2026-06-10" },
        { name: "Ndege", contact: "", company: "", stage: "Interested", value: 300000, lastContact: "2026-04-15", followUp: "2026-06-11" },
        { name: "Benard", contact: "", company: "", stage: "Follow-up", value: 180000, lastContact: "2026-04-17", followUp: "2026-06-12" },
        { name: "Joseph", contact: "", company: "", stage: "New Lead", value: 0, lastContact: "2026-04-17", followUp: "2026-06-13" }
    ];

    demoLeads.forEach(l => {
        AppState.leads.push({
            id: IDGenerator.generateLead(AppState.leads),
            name: l.name,
            contact: l.contact,
            company: l.company,
            stage: l.stage,
            value: l.value,
            lastContactDate: l.lastContact,
            followUpDate: l.followUp,
            assignedTo: 'Majani',
            source: 'Referral',
            notes: '',
            createdDate: '2026-05-26',
            activities: [],
            files: [],
            archived: false,
            converted: false,
            convertedProjectId: null
        });
    });

    // Seed a demo goal
    AppState.goals.push({
        id: IDGenerator.generateGoal(AppState.goals),
        title: "Read CIFA Notes",
        target: 300,
        current: 120,
        unit: "Pages",
        startDate: "2026-05-01",
        endDate: "2026-05-30",
        description: "Complete CIFA certification reading",
        logs: [
            { date: "2026-05-20", progress: 30, note: "Finished Chapter 1-3" },
            { date: "2026-05-25", progress: 40, note: "Finished Chapter 4" },
            { date: "2026-05-28", progress: 50, note: "Chapter 5 in progress" }
        ]
    });

    saveAll();
}

async function saveAll() {
    await Promise.all([
        Storage.save('tasks', AppState.tasks),
        Storage.save('leads', AppState.leads),
        Storage.save('projects', AppState.projects),
        Storage.save('activities', AppState.activities),
        Storage.save('goals', AppState.goals),
        Storage.save('files', AppState.files),
        Storage.save('settings', AppState.settings)
    ]);
    console.log('Task-Majani: All data saved to localStorage + IndexedDB');
}

function setDefaultDates() {
    const today = new Date().toISOString().split('T')[0];
    ['taskStart', 'taskDue', 'leadLastContact', 'leadFollowUp', 'projectStart', 'projectEnd', 'goalStart', 'goalEnd', 'logDate'].forEach(id => {
        const el = document.getElementById(id);
        if (el) el.value = today;
    });
}

// ==================== AUTHENTICATION ====================
function handleLogin() {
    const u = document.getElementById('loginUsername').value.trim().toLowerCase();
    const p = document.getElementById('loginPassword').value.trim();
    const err = document.getElementById('loginError');

    const user = DEMO_USERS[u];
    if (user && user.password === p) {
        AppState.currentUser = {
            name: user.name,
            username: u,
            role: user.role,
            roleLabel: ROLES[user.role].label
        };
        AppState.currentRole = user.role;
        Storage.save('currentUser', AppState.currentUser);
        err.style.display = 'none';
        showApp();
        showNotification('Welcome back, ' + user.name + '!', 'success');
    } else {
        err.style.display = 'block';
    }
}

function showApp() {
    document.getElementById('loginScreen').classList.add('hidden');
    document.getElementById('appContainer').classList.add('active');
    document.getElementById('userName').textContent = AppState.currentUser.name;
    document.getElementById('userAvatar').textContent = AppState.currentUser.name.charAt(0);
    document.getElementById('userRole').textContent = AppState.currentUser.roleLabel;

    const badge = document.getElementById('userRoleBadge');
    badge.textContent = AppState.currentUser.role;
    badge.className = 'role-badge role-' + AppState.currentUser.role.toLowerCase();

    applyRolePermissions();
    renderAll();
}

function handleLogout() {
    Storage.remove('currentUser');
    AppState.currentUser = null;
    AppState.currentRole = null;
    document.getElementById('loginScreen').classList.remove('hidden');
    document.getElementById('appContainer').classList.remove('active');
    document.getElementById('loginUsername').value = '';
    document.getElementById('loginPassword').value = '';
}

function applyRolePermissions() {
    const role = AppState.currentRole;
    const perms = ROLES[role]?.permissions || ['read'];
    const isReadOnly = perms.includes('read') && !perms.includes('edit') && !perms.includes('full');
    const isLimited = perms.includes('limited');

    // Hide/show action buttons based on role
    const actionButtons = document.querySelectorAll('.section-actions .btn-primary');
    actionButtons.forEach(btn => {
        if (isReadOnly) btn.style.display = 'none';
    });

    if (isReadOnly) {
        document.querySelectorAll('.data-table td:last-child').forEach(td => {
            td.style.display = 'none';
        });
    }
}

function hasPermission(permission) {
    if (!AppState.currentRole) return false;
    const perms = ROLES[AppState.currentRole]?.permissions || [];
    return perms.includes('full') || perms.includes(permission);
}

// ==================== THEME ====================
function toggleTheme() {
    const current = document.documentElement.getAttribute('data-theme') || 'light';
    const next = current === 'light' ? 'dark' : 'light';
    applyTheme(next);
    AppState.settings.theme = next;
    Storage.save('settings', AppState.settings);
}

function applyTheme(theme) {
    document.documentElement.setAttribute('data-theme', theme);
    const icon = document.getElementById('themeIcon');
    const text = document.getElementById('themeText');
    if (icon) icon.className = theme === 'dark' ? 'fas fa-sun' : 'fas fa-moon';
    if (text) text.textContent = theme === 'dark' ? 'Light Mode' : 'Dark Mode';
}

// ==================== NAVIGATION ====================
function showPage(page) {
    document.querySelectorAll('.page-section').forEach(s => s.classList.remove('active'));
    document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));

    const target = document.getElementById('page-' + page);
    if (target) target.classList.add('active');

    document.querySelectorAll('.nav-item').forEach(item => {
        const onclick = item.getAttribute('onclick');
        if (onclick && onclick.includes("'" + page + "'")) {
            item.classList.add('active');
        }
    });

    const titles = {
        dashboard: 'Dashboard', tasks: 'All Tasks', overdue: 'Overdue Alerts',
        workload: 'Team Workload', monthly: 'Monthly Analytics', yearly: 'Yearly Trends',
        performance: 'Performance Charts', projects: 'Projects', risk: 'Risk Engine',
        evm: 'EVM Financials', leads: 'Lead Tracker', pipeline: 'Sales Pipeline',
        activities: 'Activities', goals: 'Goal Tracker', calendar: 'Calendar',
        importexport: 'Import / Export'
    };
    document.getElementById('pageTitle').textContent = titles[page] || page;
    document.getElementById('breadcrumbPage').textContent = titles[page] || page;

    // Render charts for specific pages
    const chartPages = {
        dashboard: renderDashboardCharts,
        workload: renderWorkloadCharts,
        monthly: renderMonthlyChart,
        yearly: renderYearlyChart,
        performance: renderPerformanceCharts
    };
    if (chartPages[page]) setTimeout(chartPages[page], 100);

    if (page === 'calendar') renderCalendar();
    if (page === 'pipeline') renderPipeline();
    if (page === 'activities') renderActivities();
    if (page === 'goals') renderGoals();
    if (page === 'leads') renderLeadsTable();
}

// ==================== KEYBOARD SHORTCUTS ====================
function handleKeyboard(e) {
    if (e.ctrlKey && e.key === 'k') {
        e.preventDefault();
        document.getElementById('globalSearch').focus();
    }
    if (e.key === 'Escape') {
        document.querySelectorAll('.modal-overlay').forEach(m => m.classList.remove('active'));
        document.getElementById('alertsPanel').classList.remove('active');
    }
}

// ==================== NOTIFICATIONS ====================
function showNotification(message, type = 'info') {
    const notif = document.getElementById('notification');
    notif.textContent = message;
    notif.className = 'notification ' + type;
    notif.classList.add('show');
    setTimeout(() => notif.classList.remove('show'), 3000);
}

function toggleAlertsPanel() {
    document.getElementById('alertsPanel').classList.toggle('active');
}

// ==================== MASTER RENDER ====================
function renderAll() {
    updateStats();
    renderTasksTable();
    renderOverdueTable();
    renderWorkloadTable();
    renderMonthlyTable();
    renderYearlyTable();
    renderProjectTable();
    renderRiskTable();
    renderEvmTable();
    renderLeadsTable();
    renderCallsTable(); // Legacy compatibility
    renderDashboardOverdue();
    renderDashboardActivities();
    renderGoals();
    updateNavBadges();
    generateAlerts();
}

// ==================== STATS & DASHBOARD ====================
function updateStats() {
    const tasks = AppState.tasks;
    const total = tasks.length;
    const inProgress = tasks.filter(t => t.status === 'In Progress').length;
    const done = tasks.filter(t => t.status === 'Done').length;
    const notStarted = tasks.filter(t => t.status === 'Not Started').length;
    const overdue = tasks.filter(t => t.daysOverdue > 0).length;
    const high = tasks.filter(t => t.priority === 'High').length;
    const critical = tasks.filter(t => t.priority === 'Critical').length;

    document.getElementById('dashTotal').textContent = total;
    document.getElementById('dashProgress').textContent = inProgress;
    document.getElementById('dashDone').textContent = done;
    document.getElementById('dashNotStarted').textContent = notStarted;
    document.getElementById('dashOverdue').textContent = overdue;
    document.getElementById('dashHigh').textContent = high + critical;

    // Dashboard widgets
    const today = new Date().toISOString().split('T')[0];
    const todayTasks = tasks.filter(t => t.dueDate === today).length;
    const followupsDue = AppState.leads.filter(l => l.followUpDate === today && !l.archived && !l.converted).length;
    const activeGoals = AppState.goals.filter(g => new Date(g.endDate) >= new Date()).length;
    const overdueProjects = new Set(tasks.filter(t => t.daysOverdue > 0).map(t => t.project)).size;

    document.getElementById('dashTodayTasks').textContent = todayTasks;
    document.getElementById('dashFollowups').textContent = followupsDue;
    document.getElementById('dashGoals').textContent = activeGoals;
    document.getElementById('dashOverdueProjects').textContent = overdueProjects;

    // Quick actions
    document.getElementById('dashOverdueQuickText').textContent = overdue + ' tasks need attention';
    document.getElementById('dashOverdueQuickCount').textContent = overdue;
    const owners = new Set(tasks.map(t => t.owner)).size;
    document.getElementById('dashWorkloadText').textContent = owners + ' team members';
    document.getElementById('dashWorkloadCount').textContent = owners;
    const activeLeads = AppState.leads.filter(l => !l.archived && !l.converted).length;
    document.getElementById('dashLeadsText').textContent = activeLeads + ' active leads';
    document.getElementById('dashLeadsCount').textContent = activeLeads;

    // Overdue page
    document.getElementById('overdueTotal').textContent = overdue;
    const maxOd = tasks.filter(t => t.daysOverdue > 0).reduce((max, t) => Math.max(max, t.daysOverdue || 0), 0);
    document.getElementById('overdueMax').textContent = maxOd;
    const affectedOwners = new Set(tasks.filter(t => t.daysOverdue > 0).map(t => t.owner)).size;
    document.getElementById('overdueOwners').textContent = affectedOwners;

    // Export page
    document.getElementById('expTotalTasks').textContent = total;
    document.getElementById('expTotalOwners').textContent = owners;
    document.getElementById('expTotalProjects').textContent = new Set(tasks.map(t => t.project)).size;
    document.getElementById('expTotalLeads').textContent = AppState.leads.length;
    document.getElementById('expTotalGoals').textContent = AppState.goals.length;
    document.getElementById('expTotalActivities').textContent = AppState.activities.length;
}

function updateNavBadges() {
    const tasks = AppState.tasks;
    document.getElementById('navTotalTasks').textContent = tasks.length;
    document.getElementById('navOverdue').textContent = tasks.filter(t => t.daysOverdue > 0).length;
    document.getElementById('navWorkload').textContent = new Set(tasks.map(t => t.owner)).size;
    document.getElementById('navProjects').textContent = new Set(tasks.map(t => t.project)).size;
    document.getElementById('navLeads').textContent = AppState.leads.filter(l => !l.archived).length;
    document.getElementById('navActivities').textContent = AppState.activities.length;

    const hasOverdue = tasks.some(t => t.daysOverdue > 0);
    document.getElementById('notifDot').style.display = hasOverdue ? 'block' : 'none';
}

function renderDashboardOverdue() {
    const tbody = document.getElementById('dashOverdueTable');
    const overdue = AppState.tasks
        .filter(t => t.daysOverdue > 0)
        .sort((a, b) => (b.daysOverdue || 0) - (a.daysOverdue || 0))
        .slice(0, 5);

    tbody.innerHTML = overdue.map(t => `
        <tr>
            <td>${t.name || ''}</td>
            <td>${t.owner || ''}</td>
            <td><span class="badge badge-${t.priority === 'High' ? 'high' : t.priority === 'Critical' ? 'critical' : 'medium'}">${t.priority || ''}</span></td>
            <td><span class="badge badge-overdue">${t.daysOverdue} days</span></td>
            <td><span class="badge badge-${t.status === 'In Progress' ? 'progress' : 'not-started'}">${t.status || ''}</span></td>
        </tr>
    `).join('');
}

function renderDashboardActivities() {
    const container = document.getElementById('dashRecentActivities');
    const recent = [...AppState.activities]
        .sort((a, b) => new Date(b.timestamp) - new Date(a.timestamp))
        .slice(0, 5);

    if (recent.length === 0) {
        container.innerHTML = '<p class="text-muted" style="text-align:center;padding:20px;">No recent activities</p>';
        return;
    }

    container.innerHTML = '<div class="timeline">' + recent.map(a => {
        const date = new Date(a.timestamp);
        const dateStr = date.toLocaleDateString('en-GB', { day: 'numeric', month: 'short', year: 'numeric' });
        const timeStr = date.toLocaleTimeString('en-GB', { hour: '2-digit', minute: '2-digit' });
        return `
            <div class="timeline-item">
                <div class="timeline-dot ${a.type.toLowerCase()}"></div>
                <div class="timeline-content">
                    <div class="timeline-header">
                        <span class="timeline-date">${dateStr} - ${timeStr}</span>
                        <span class="timeline-type ${a.type.toLowerCase()}">${a.type}</span>
                    </div>
                    <div class="timeline-text">${a.note}</div>
                    <div class="timeline-user">by ${a.user} on ${a.entityType} ${a.entityId}</div>
                </div>
            </div>
        `;
    }).join('') + '</div>';
}

// ==================== TASK CRUD ====================
function openAddTaskModal() {
    if (!hasPermission('edit')) { showNotification('Read-only access', 'error'); return; }
    document.getElementById('taskModalTitle').innerHTML = '<i class="fas fa-plus-circle"></i> Add New Task';
    document.getElementById('taskEditId').value = '';
    document.getElementById('taskId').value = IDGenerator.generateTask(AppState.tasks);
    document.getElementById('taskProject').value = '';
    document.getElementById('taskName').value = '';
    document.getElementById('taskOwner').value = AppState.currentUser?.name || '';
    document.getElementById('taskPriority').value = 'High';
    document.getElementById('taskStatus').value = 'In Progress';
    document.getElementById('taskPercent').value = 0;
    document.getElementById('taskDependsOn').value = '';
    document.getElementById('taskFileList').innerHTML = '';
    document.getElementById('taskActivityPreview').innerHTML = '';
    setDefaultDates();
    document.getElementById('taskModal').classList.add('active');
}

function openEditTaskModal(id) {
    if (!hasPermission('edit')) { showNotification('Read-only access', 'error'); return; }
    const task = AppState.tasks.find(t => t.id === id);
    if (!task) return;

    document.getElementById('taskModalTitle').innerHTML = '<i class="fas fa-edit"></i> Edit Task';
    document.getElementById('taskEditId').value = task.id;
    document.getElementById('taskId').value = task.id;
    document.getElementById('taskProject').value = task.project || '';
    document.getElementById('taskName').value = task.name || '';
    document.getElementById('taskOwner').value = task.owner || '';
    document.getElementById('taskPriority').value = task.priority || 'Medium';
    document.getElementById('taskStatus').value = task.status || 'Not Started';
    document.getElementById('taskPercent').value = task.percentComplete || 0;
    document.getElementById('taskStart').value = task.startDate || '';
    document.getElementById('taskDue').value = task.dueDate || '';
    document.getElementById('taskDependsOn').value = task.dependsOn || '';

    // Render file list
    renderFileList('task', task.files || []);
    // Render activities
    renderEntityActivities('task', task.id, 'taskActivityPreview');

    document.getElementById('taskModal').classList.add('active');
}

function saveTask() {
    if (!hasPermission('edit')) { showNotification('Read-only access', 'error'); return; }

    const editId = document.getElementById('taskEditId').value;
    const name = document.getElementById('taskName').value.trim();
    if (!name) { showNotification('Task name is required!', 'error'); return; }

    const taskData = {
        id: editId || document.getElementById('taskId').value,
        name: name,
        project: document.getElementById('taskProject').value.trim(),
        owner: document.getElementById('taskOwner').value.trim(),
        priority: document.getElementById('taskPriority').value,
        status: document.getElementById('taskStatus').value,
        percentComplete: parseInt(document.getElementById('taskPercent').value) || 0,
        startDate: document.getElementById('taskStart').value,
        dueDate: document.getElementById('taskDue').value,
        dependsOn: document.getElementById('taskDependsOn').value.trim() || null,
        daysOverdue: 0,
        signal: '🟢 NEW',
        month: new Date().toLocaleString('default', { month: 'short' }),
        year: new Date().getFullYear(),
        createdDate: new Date().toISOString().split('T')[0]
    };

    // Calculate days overdue
    if (taskData.dueDate) {
        const due = new Date(taskData.dueDate);
        const today = new Date();
        const diff = Math.floor((today - due) / (1000 * 60 * 60 * 24));
        taskData.daysOverdue = diff > 0 ? diff : 0;
        if (taskData.daysOverdue > 0) taskData.signal = '🚨 OVERDUE';
        else if (taskData.percentComplete >= 80) taskData.signal = '📈 AHEAD';
        else taskData.signal = '📉 SLIPPAGE';
    }

    // Check dependency
    if (taskData.dependsOn) {
        const dep = AppState.tasks.find(t => t.id === taskData.dependsOn);
        if (dep && dep.status !== 'Done') {
            showNotification('Warning: This task depends on ' + dep.id + ' which is not completed', 'warning');
        }
    }

    if (editId) {
        const idx = AppState.tasks.findIndex(t => t.id === editId);
        if (idx >= 0) {
            AppState.tasks[idx] = { ...AppState.tasks[idx], ...taskData };
        }
    } else {
        AppState.tasks.push({ ...taskData, activities: [], files: [] });
    }

    saveAll();
    renderAll();
    closeModal('taskModal');
    showNotification('Task saved successfully!', 'success');
}

function deleteTask(id) {
    if (!hasPermission('edit')) { showNotification('Read-only access', 'error'); return; }
    if (!confirm('Are you sure you want to delete this task?')) return;
    AppState.tasks = AppState.tasks.filter(t => t.id !== id);
    saveAll();
    renderAll();
    showNotification('Task deleted!', 'success');
}

function renderTasksTable() {
    const tbody = document.getElementById('tasksTableBody');
    const filterStatus = document.getElementById('filterStatus').value;
    const filterPriority = document.getElementById('filterPriority').value;
    const filterOwner = document.getElementById('filterOwner').value;
    const filterProject = document.getElementById('filterProject').value;
    const filterDue = document.getElementById('filterDueDate').value;
    const search = document.getElementById('globalSearch').value.toLowerCase();

    const today = new Date().toISOString().split('T')[0];
    const weekEnd = new Date(); weekEnd.setDate(weekEnd.getDate() + 7);
    const monthEnd = new Date(); monthEnd.setMonth(monthEnd.getMonth() + 1);

    let filtered = AppState.tasks.filter(t => {
        if (filterStatus && t.status !== filterStatus) return false;
        if (filterPriority && t.priority !== filterPriority) return false;
        if (filterOwner && t.owner !== filterOwner) return false;
        if (filterProject && t.project !== filterProject) return false;
        if (filterDue) {
            if (filterDue === 'overdue' && t.daysOverdue <= 0) return false;
            if (filterDue === 'today' && t.dueDate !== today) return false;
            if (filterDue === 'week' && (t.dueDate < today || t.dueDate > weekEnd.toISOString().split('T')[0])) return false;
            if (filterDue === 'month' && (t.dueDate < today || t.dueDate > monthEnd.toISOString().split('T')[0])) return false;
        }
        if (search) {
            const text = (t.name + ' ' + t.project + ' ' + t.owner + ' ' + t.id).toLowerCase();
            if (!text.includes(search)) return false;
        }
        return true;
    });

    tbody.innerHTML = filtered.map(t => {
        const depWarning = t.dependsOn ? (AppState.tasks.find(d => d.id === t.dependsOn)?.status !== 'Done' ? '<i class="fas fa-lock text-danger" title="Blocked by ' + t.dependsOn + '"></i> ' : '') : '';
        return `
        <tr>
            <td><strong>${t.id || '-'}</strong></td>
            <td>${t.project || ''}</td>
            <td>${depWarning}${t.name || ''}</td>
            <td><span class="badge badge-${t.priority === 'High' ? 'high' : t.priority === 'Critical' ? 'critical' : t.priority === 'Medium' ? 'medium' : 'low'}">${t.owner || ''}</span></td>
            <td><span class="badge badge-${t.status === 'In Progress' ? 'progress' : t.status === 'Done' ? 'done' : t.status === 'Not Started' ? 'not-started' : 'overdue'}">${t.status || ''}</span></td>
            <td><span class="badge badge-${t.priority === 'High' ? 'high' : t.priority === 'Critical' ? 'critical' : t.priority === 'Medium' ? 'medium' : 'low'}">${t.priority || ''}</span></td>
            <td>${t.dueDate || ''}</td>
            <td>
                <div class="progress-bar"><div class="progress-fill ${t.percentComplete >= 80 ? 'green' : t.percentComplete >= 50 ? 'blue' : t.percentComplete >= 20 ? 'orange' : 'red'}" style="width:${t.percentComplete || 0}%"></div></div>
                <div class="progress-text">${t.percentComplete || 0}%</div>
            </td>
            <td>${t.signal || ''}</td>
            <td>
                <button class="header-btn" onclick="openEditTaskModal('${t.id}')" title="Edit"><i class="fas fa-edit"></i></button>
                <button class="header-btn" onclick="deleteTask('${t.id}')" title="Delete" style="margin-left:4px;"><i class="fas fa-trash"></i></button>
                <button class="header-btn" onclick="openAddActivityModal('task', '${t.id}')" title="Add Activity" style="margin-left:4px;"><i class="fas fa-comment"></i></button>
            </td>
        </tr>
    `}).join('');
}

function filterTasks() { renderTasksTable(); }

function populateFilters() {
    const owners = [...new Set(AppState.tasks.map(t => t.owner).filter(Boolean))].sort();
    const projects = [...new Set(AppState.tasks.map(t => t.project).filter(Boolean))].sort();
    const leadAssignees = [...new Set(AppState.leads.map(l => l.assignedTo).filter(Boolean))].sort();

    const ownerSel = document.getElementById('filterOwner');
    const projSel = document.getElementById('filterProject');
    const leadAssignSel = document.getElementById('filterLeadAssigned');

    if (ownerSel) {
        ownerSel.innerHTML = '<option value="">All Owners</option>';
        owners.forEach(o => { const opt = document.createElement('option'); opt.value = o; opt.textContent = o; ownerSel.appendChild(opt); });
    }
    if (projSel) {
        projSel.innerHTML = '<option value="">All Projects</option>';
        projects.forEach(p => { const opt = document.createElement('option'); opt.value = p; opt.textContent = p; projSel.appendChild(opt); });
    }
    if (leadAssignSel) {
        leadAssignSel.innerHTML = '<option value="">All Assigned</option>';
        leadAssignees.forEach(a => { const opt = document.createElement('option'); opt.value = a; opt.textContent = a; leadAssignSel.appendChild(opt); });
    }
}

function handleSearch() {
    renderTasksTable();
    filterLeads();
}

// ==================== OVERDUE ====================
function renderOverdueTable() {
    const tbody = document.getElementById('overdueTableBody');
    const overdue = AppState.tasks
        .filter(t => t.daysOverdue > 0)
        .sort((a, b) => (b.daysOverdue || 0) - (a.daysOverdue || 0));

    tbody.innerHTML = overdue.map(t => `
        <tr>
            <td><strong>${t.id || '-'}</strong></td>
            <td>${t.name || ''}</td>
            <td>${t.owner || ''}</td>
            <td>${t.project || ''}</td>
            <td><span class="badge badge-${t.priority === 'High' ? 'high' : t.priority === 'Critical' ? 'critical' : 'medium'}">${t.priority || ''}</span></td>
            <td><span class="badge badge-overdue">${t.daysOverdue} days</span></td>
            <td>${t.dueDate || ''}</td>
            <td>
                <div class="progress-bar"><div class="progress-fill red" style="width:${t.percentComplete || 0}%"></div></div>
                <div class="progress-text">${t.percentComplete || 0}%</div>
            </td>
            <td><span class="badge badge-${t.status === 'In Progress' ? 'progress' : 'not-started'}">${t.status || ''}</span></td>
        </tr>
    `).join('');
}
