<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>AyurSutra — Admin Dashboard (Interactive Prototype)</title>
  <meta name="description" content="Interactive HTML/CSS/JS prototype for AyurSutra admin: patient management, therapy scheduling, progress tracking, notifications and feedback. All data stored locally in the browser (no backend)." />

  <style>
    :root{
      --bg:#f6f9f8; --card:#ffffff; --muted:#6b7280; --accent:#05a57a; --accent-2:#28c76f; --accent-3:#ffa94d; --danger:#ef4444; --radius:12px; --panel:#fbfdfc; --maxw:1200px; --text:#0f1724; font-family:Inter, system-ui, -apple-system, 'Segoe UI', Roboto, 'Helvetica Neue', Arial;
    }
    *{box-sizing:border-box}
    body{margin:0;background:linear-gradient(180deg,#f3fbf8, #f6f9fb);color:var(--text);}
    .wrap{max-width:var(--maxw);margin:20px auto;padding:20px}

    header{display:flex;align-items:center;justify-content:space-between;gap:16px;padding:10px 0}
    .brand{display:flex;align-items:center;gap:12px}
    .logo{width:48px;height:48px;border-radius:12px;background:linear-gradient(135deg,var(--accent),var(--accent-2));display:flex;align-items:center;justify-content:center;color:white;font-weight:800}
    .title{font-size:20px;font-weight:700}
    .subtitle{font-size:13px;color:var(--muted)}

    nav{display:flex;gap:10px;margin-top:18px}
    .tab{padding:10px 16px;border-radius:30px;background:transparent;border:1px solid transparent;color:var(--muted);cursor:pointer}
    .tab.active{background:var(--panel);color:var(--text);border-color:rgba(15,23,36,0.04);box-shadow:0 1px 0 rgba(15,23,36,0.02)}

    .top-row{display:flex;justify-content:space-between;align-items:center;margin-top:18px}
    .metrics{display:flex;gap:12px;flex-wrap:wrap}
    .metric{background:var(--card);padding:16px;border-radius:12px;min-width:140px;box-shadow:0 6px 18px rgba(15,23,36,0.03);border:1px solid rgba(15,23,36,0.03)}
    .metric h4{margin:0;font-size:22px}
    .metric p{margin:6px 0 0 0;color:var(--muted);font-size:13px}

    .content{display:grid;grid-template-columns:1fr;gap:16px;margin-top:16px}

    /* Patient table */
    .card{background:var(--card);padding:18px;border-radius:12px;box-shadow:0 6px 18px rgba(15,23,36,0.03);border:1px solid rgba(15,23,36,0.03)}
    .table{width:100%;border-collapse:collapse}
    .table th,.table td{padding:12px 10px;text-align:left;border-bottom:1px solid rgba(15,23,36,0.04)}
    .table th{color:var(--muted);font-weight:600;font-size:13px}

    .btn{background:linear-gradient(90deg,var(--accent),var(--accent-2));color:white;padding:8px 12px;border-radius:10px;border:none;cursor:pointer}
    .btn.ghost{background:transparent;border:1px solid rgba(15,23,36,0.06);color:var(--text)}
    .btn.small{padding:6px 8px;font-size:13px}

    .right-actions{display:flex;gap:8px;align-items:center}

    /* Schedule list */
    .session{padding:12px;border-radius:10px;background:linear-gradient(180deg,#fff,#fbfdfd);border:1px solid rgba(15,23,36,0.03);display:flex;justify-content:space-between;align-items:center}
    .session .info{display:flex;gap:12px;align-items:center}
    .badge{padding:4px 8px;border-radius:999px;background:#eef9f5;color:var(--accent);font-weight:700;font-size:13px}

    /* Notifications / Feedback */
    .list{display:flex;flex-direction:column;gap:12px}
    .list-item{padding:12px;border-radius:10px;background:#fff;border:1px solid rgba(15,23,36,0.03)}
    .muted{color:var(--muted)}

    /* Forms / modal */
    .modal-backdrop{position:fixed;inset:0;background:rgba(2,6,23,0.45);display:none;align-items:center;justify-content:center;padding:20px;z-index:60}
    .modal{background:var(--card);width:100%;max-width:720px;border-radius:12px;padding:18px}
    .form-row{display:flex;gap:12px}
    .form-field{display:flex;flex-direction:column;flex:1}
    input[type=text],input[type=email],select,textarea,input[type=date],input[type=time]{padding:10px;border-radius:8px;border:1px solid rgba(15,23,36,0.06);background:transparent}
    label{font-size:13px;color:var(--muted);margin-bottom:6px}

    /* simple chart area */
    .chart-wrap{height:320px;padding:12px;border-radius:10px;background:linear-gradient(180deg,#fff,#fbfdfd);border:1px solid rgba(15,23,36,0.03)}

    footer{margin-top:18px;color:var(--muted);font-size:13px;text-align:center}

    @media (min-width:960px){
      .content{grid-template-columns:2fr 1fr}
      .top-row{align-items:flex-end}
    }

  </style>
</head>
<body>
  <div class="wrap">
    <header>
      <div class="brand">
        <div class="logo">AS</div>
        <div>
          <div class="title">AyurSutra</div>
          <div class="subtitle">Panchakarma Management System</div>
        </div>
      </div>
      <div class="right-actions">
        <button id="langToggle" class="btn.ghost btn small">English</button>
        <div style="display:flex;align-items:center;gap:8px">
          <div style="text-align:right;font-size:13px;color:var(--muted)">Admin<br><strong>Admin</strong></div>
          <button id="logout" class="btn ghost">Logout</button>
        </div>
      </div>
    </header>

    <nav>
      <button class="tab active" data-tab="dashboard">Dashboard</button>
      <button class="tab" data-tab="patients">Patient Management</button>
      <button class="tab" data-tab="scheduling">Therapy Scheduling</button>
      <button class="tab" data-tab="progress">Progress Tracking</button>
      <button class="tab" data-tab="notifications">Notifications</button>
      <button class="tab" data-tab="feedback">Feedback</button>
    </nav>

    <div class="top-row">
      <div class="metrics">
        <div class="metric"><h4 id="m-patients">0</h4><p>Total Patients</p></div>
        <div class="metric"><h4 id="m-active">0</h4><p>Active Treatments</p></div>
        <div class="metric"><h4 id="m-sessions">0</h4><p>Today's Sessions</p></div>
        <div class="metric"><h4 id="m-alerts">0</h4><p>Pending Alerts</p></div>
      </div>

      <div class="right-actions">
        <button id="addPatientBtn" class="btn">+ Add Patient</button>
      </div>
    </div>

    <main style="margin-top:12px">
      <div class="content">
        <!-- LEFT / MAIN -->
        <div>
          <!-- Dashboard area -->
          <section id="dashboard" class="card view">
            <h2>Welcome to AyurSutra, Admin</h2>
            <p class="muted">Manage your Panchakarma practice with comprehensive patient care tools</p>

            <div style="margin-top:16px;display:flex;gap:12px;flex-wrap:wrap">
              <div class="card" style="flex:1;min-width:220px">
                <h4 style="margin:0">Recent Activities</h4>
                <div class="list" id="recentActivities" style="margin-top:10px"></div>
              </div>

              <div class="card" style="flex:1;min-width:220px">
                <h4 style="margin:0">Quick Actions</h4>
                <div style="display:flex;flex-direction:column;gap:8px;margin-top:10px">
                  <button class="btn ghost" id="quickSchedule">Schedule</button>
                  <button class="btn ghost" id="quickNotify">Notify</button>
                </div>
              </div>
            </div>
          </section>

          <!-- Patients -->
          <section id="patients" class="card view" style="display:none">
            <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:10px">
              <h3>Manage Patients</h3>
              <div class="muted">Patient List <span id="badgeCount"></span></div>
            </div>

            <table class="table" id="patientsTable">
              <thead>
                <tr><th>Patient Name</th><th>Contact</th><th>Age</th><th>Status</th><th>Join Date</th><th>Last Visit</th><th>Actions</th></tr>
              </thead>
              <tbody></tbody>
            </table>
          </section>

          <!-- Scheduling -->
          <section id="scheduling" class="card view" style="display:none">
            <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:10px">
              <h3>Therapy Scheduling</h3>
              <button class="btn" id="openSchedule">+ Schedule Therapy</button>
            </div>

            <div class="list" id="sessionsList"></div>
          </section>

          <!-- Progress Tracking -->
          <section id="progress" class="card view" style="display:none">
            <h3>Progress Tracking</h3>
            <div style="display:flex;gap:12px;margin-top:12px;flex-wrap:wrap">
              <div class="metric"><h4 id="completedSessions">0/0</h4><p>Completed Sessions</p></div>
              <div class="metric"><h4 id="improvement">0%</h4><p>Improvement</p></div>
              <div class="metric"><h4 id="nextSession">-</h4><p>Next Session</p></div>
              <div class="metric"><h4 id="therapyType">-</h4><p>Therapy Type</p></div>
            </div>

            <div style="margin-top:16px" class="chart-wrap">
              <canvas id="progressChart" width="800" height="260"></canvas>
            </div>
          </section>

          <!-- Notifications -->
          <section id="notifications" class="card view" style="display:none">
            <h3>Notifications</h3>
            <div style="display:flex;justify-content:space-between;align-items:center;margin-top:8px">
              <div class="muted">Notification History</div>
              <button class="btn" id="sendNotification">Send Notification</button>
            </div>
            <div class="list" id="notificationsList" style="margin-top:12px"></div>
          </section>

          <!-- Feedback -->
          <section id="feedback" class="card view" style="display:none">
            <h3>Feedback</h3>
            <div style="display:flex;gap:12px;margin-top:12px;flex-wrap:wrap">
              <div class="metric"><h4 id="totalFeedback">0</h4><p>Total Feedback</p></div>
              <div class="metric"><h4 id="avgSatisfaction">0</h4><p>Avg Satisfaction</p></div>
              <div class="metric"><h4 id="actionRequired">0</h4><p>Action Required</p></div>
            </div>

            <div class="list" id="feedbackList" style="margin-top:12px"></div>
          </section>
        </div>

        <!-- RIGHT / SIDEBAR -->
        <aside>
          <div class="card">
            <h4 style="margin:0">Patients</h4>
            <div id="miniList" style="margin-top:12px;display:flex;flex-direction:column;gap:8px"></div>
          </div>

          <div class="card" style="margin-top:12px">
            <h4 style="margin:0">AI Chatbot (Demo)</h4>
            <div id="chatLog" style="margin-top:10px;display:flex;flex-direction:column;gap:8px;height:220px;overflow:auto;padding-right:8px"></div>
            <div style="display:flex;gap:8px;margin-top:8px">
              <input id="chatInput" type="text" placeholder="Ask AI (demo)" style="flex:1;padding:8px;border-radius:8px;border:1px solid rgba(15,23,36,0.06)" />
              <button id="sendChat" class="btn">Send</button>
            </div>
          </div>

          <div class="card" style="margin-top:12px">
            <h4 style="margin:0">AI Report Maker</h4>
            <div style="margin-top:8px;display:flex;flex-direction:column;gap:8px">
              <select id="reportPatient"></select>
              <select id="reportType"><option>Discharge Summary</option><option>Progress Note</option></select>
              <textarea id="reportObservations" placeholder="Key observations..." style="min-height:80px;padding:8px;border-radius:8px;border:1px solid rgba(15,23,36,0.06)"></textarea>
              <div style="display:flex;gap:8px"><button id="generateReport" class="btn">Generate Report</button><button id="downloadReport" class="btn ghost">Download</button></div>
            </div>
          </div>
        </aside>
      </div>

      <footer>Prototype — everything works client-side using LocalStorage. I can convert this to React/Next or add backend integration.</footer>
    </main>
  </div>

  <!-- Modal: Add/Edit Patient -->
  <div id="modal" class="modal-backdrop">
    <div class="modal" role="dialog" aria-modal="true">
      <h3 id="modalTitle">Add Patient</h3>
      <div style="margin-top:12px;display:flex;flex-direction:column;gap:10px">
        <div class="form-row">
          <div class="form-field"><label>Name</label><input id="p_name" type="text" /></div>
          <div class="form-field"><label>Age</label><input id="p_age" type="text" /></div>
        </div>
        <div class="form-row">
          <div class="form-field"><label>Contact (phone)</label><input id="p_phone" type="text" /></div>
          <div class="form-field"><label>Email</label><input id="p_email" type="email" /></div>
        </div>
        <div class="form-row">
          <div class="form-field"><label>Therapy Type</label><select id="p_therapy">
                <option>Panchakarma</option>
                <option>Abhyanga (Full body massage)</option>
                <option>Shirodhara</option>
                <option>Shiroabhyanga</option>
                <option>Kati Basti</option>
                <option>Janu Basti</option>
                <option>Netra Tarpana</option>
                <option>Swedana (Steam)</option>
                <option>Pinda Sweda</option>
                <option>Udvartana</option>
                <option>Pizhichil</option>
                <option>Navarakizhi</option>
                <option>Karnapoorana</option>
              </select></div>
          <div class="form-field"><label>Join Date</label><input id="p_join" type="date" /></div>
        </div>

        <div style="display:flex;gap:8px;justify-content:flex-end;margin-top:6px">
          <button id="closeModal" class="btn ghost">Cancel</button>
          <button id="savePatient" class="btn">Save</button>
        </div>
      </div>
    </div>
  </div>

  <!-- Modal: Schedule Therapy -->
  <div id="modalSchedule" class="modal-backdrop">
    <div class="modal">
      <h3>Schedule Therapy</h3>
      <div style="margin-top:12px;display:flex;flex-direction:column;gap:10px">
        <div class="form-row">
          <div class="form-field"><label>Patient</label><select id="s_patient"></select></div>
          <div class="form-field"><label>Therapy Type</label><select id="s_type">
          <option>Abhyanga (Full body massage)</option>
          <option>Shirodhara</option>
          <option>Shiroabhyanga</option>
          <option>Kati Basti</option>
          <option>Janu Basti</option>
          <option>Netra Tarpana</option>
          <option>Swedana (Steam)</option>
          <option>Pinda Sweda</option>
          <option>Udvartana</option>
          <option>Pizhichil</option>
          <option>Navarakizhi</option>
          <option>Karnapoorana</option>
        </select></div>
        </div>
        <div class="form-row">
          <div class="form-field"><label>Date</label><input id="s_date" type="date" /></div>
          <div class="form-field"><label>Time</label><input id="s_time" type="time" /></div>
        </div>
        <div style="display:flex;gap:8px;justify-content:flex-end">
          <button id="closeSchedule" class="btn ghost">Cancel</button>
          <button id="saveSchedule" class="btn">Schedule</button>
        </div>
      </div>
    </div>
  </div>

  <script>
    // Simple client-side app using localStorage. All actions work locally.
    const STORAGE_KEY = 'ayursutra_data_v1';

    // Default sample data
    const defaultData = {
      patients: [
        {id: 1, name: 'Ravi Kumar', phone: '+91-9876543210', email: 'ravi@example.com', age:45, status:'active', join:'2024-01-15', lastVisit:'2024-02-20', therapy:'Panchakarma'},
        {id: 2, name: 'Priya Sharma', phone: '+91-9876543212', email: 'priya@example.com', age:32, status:'active', join:'2024-02-01', lastVisit:'2024-02-18', therapy:'Abhyanga'}
      ],
      sessions: [
        {id:1, patientId:1, patientName:'Ravi Kumar', date:'2024-12-15', time:'10:00', type:'Abhyanga', status:'scheduled', practitioner:'Dr. Priya Sharma'},
        {id:2, patientId:2, patientName:'Sunita Devi', date:'2024-12-15', time:'14:00', type:'Shirodhara', status:'completed', practitioner:'Dr. Meera'}
      ],
      notifications: [
        {id:1, to:'Amit Kumar', channel:'SMS', status:'delivered', message:'Please fast for 12 hours before your Panchakarma session tomorrow. Drink plenty of water today.', sent:'2024-12-14 18:00'},
      ],
      feedback: [
        {id:1, patient:'Amit Kumar', session:15, date:'2024-12-13', pain:2, energy:8, satisfaction:5, symptoms:['Reduced joint pain'], improvements:['Increased mobility'], sideEffects:['Mild fatigue initially'], notes:'Feeling much better overall.'}
      ],
      activities: ['New patient registered: Meera Sharma - Arthritis Treatment','Session scheduled: Abhyanga for Rajesh Singh','Feedback received: 5-star rating from Amit Kumar']
    };

    // Load or init
    function loadData(){
      const raw = localStorage.getItem(STORAGE_KEY);
      if(!raw) { localStorage.setItem(STORAGE_KEY, JSON.stringify(defaultData)); return JSON.parse(JSON.stringify(defaultData)); }
      try { return JSON.parse(raw); } catch(e){ localStorage.setItem(STORAGE_KEY, JSON.stringify(defaultData)); return defaultData; }
    }
    function saveData(){ localStorage.setItem(STORAGE_KEY, JSON.stringify(state)); renderAll(); }

    let state = loadData();

    // DOM refs
    const tabs = document.querySelectorAll('.tab');
    const views = document.querySelectorAll('.view');
    const patientsTbody = document.querySelector('#patientsTable tbody');
    const badgeCount = document.getElementById('badgeCount');
    const miniList = document.getElementById('miniList');
    const mPatients = document.getElementById('m-patients');
    const mActive = document.getElementById('m-active');
    const mSessions = document.getElementById('m-sessions');
    const mAlerts = document.getElementById('m-alerts');
    const recentActivities = document.getElementById('recentActivities');
    const sessionsList = document.getElementById('sessionsList');
    const notificationsList = document.getElementById('notificationsList');
    const feedbackList = document.getElementById('feedbackList');
    const totalFeedback = document.getElementById('totalFeedback');
    const avgSatisfaction = document.getElementById('avgSatisfaction');
    const actionRequired = document.getElementById('actionRequired');

    // Modals
    const modal = document.getElementById('modal');
    const modalSchedule = document.getElementById('modalSchedule');

    // Forms
    const addPatientBtn = document.getElementById('addPatientBtn');
    const savePatientBtn = document.getElementById('savePatient');
    const closeModalBtn = document.getElementById('closeModal');

    const openScheduleBtn = document.getElementById('openSchedule');
    const saveScheduleBtn = document.getElementById('saveSchedule');
    const closeScheduleBtn = document.getElementById('closeSchedule');

    // Chat
    const chatLog = document.getElementById('chatLog');
    const chatInput = document.getElementById('chatInput');
    const sendChatBtn = document.getElementById('sendChat');

    // Report
    const reportPatient = document.getElementById('reportPatient');
    const generateReportBtn = document.getElementById('generateReport');
    const downloadReportBtn = document.getElementById('downloadReport');

    // Chart
    const chartCanvas = document.getElementById('progressChart');
    const ctx = chartCanvas.getContext('2d');

    // utilities
    function uid(){ return Date.now() + Math.floor(Math.random()*999); }

    // Tab switching
    tabs.forEach(t=>t.addEventListener('click', ()=>{
      tabs.forEach(x=>x.classList.remove('active'));
      t.classList.add('active');
      const key = t.dataset.tab;
      views.forEach(v=>v.style.display = v.id===key? 'block': 'none');
      renderAll();
    }));

    // Render patients table
    function renderPatients(){
      patientsTbody.innerHTML = '';
      state.patients.forEach(p=>{
        const tr = document.createElement('tr');
        tr.innerHTML = `<td><strong>${p.name}</strong><div class="muted">${p.email||''}</div></td>
                        <td>${p.phone||''}</td>
                        <td>${p.age||''}</td>
                        <td><span class="badge">${p.status||'active'}</span></td>
                        <td>${p.join||''}</td>
                        <td>${p.lastVisit||''}</td>
                        <td>
                          <button class="btn small ghost" data-id="${p.id}" data-action="edit">✎</button>
                          <button class="btn small ghost" data-id="${p.id}" data-action="delete">🗑</button>
                        </td>`;
        patientsTbody.appendChild(tr);
      });
      badgeCount.textContent = ` ${state.patients.length} patients `;
      // wire actions
      patientsTbody.querySelectorAll('button').forEach(btn=>{
        btn.addEventListener('click', (e)=>{
          const id = Number(btn.dataset.id);
          const action = btn.dataset.action;
          if(action==='edit') openPatientModal(id);
          if(action==='delete') { if(confirm('Delete patient?')){ state.patients = state.patients.filter(x=>x.id!==id); state.activities.unshift('Patient deleted'); saveData(); }}
        })
      });
    }

    // Mini list
    function renderMiniList(){
      miniList.innerHTML = '';
      state.patients.slice(0,6).forEach(p=>{
        const el = document.createElement('div'); el.className='list-item'; el.innerHTML = `<strong>${p.name}</strong><div class="muted">${p.therapy} • ${p.join||''}</div>`;
        miniList.appendChild(el);
      });
    }

    // Sessions
    function renderSessions(){
      sessionsList.innerHTML = '';
      state.sessions.forEach(s=>{
        const row = document.createElement('div'); row.className='session';
        row.innerHTML = `<div class="info"><div><strong>${s.patientName}</strong> <span class="muted">${s.type}</span></div><div class="muted">📅 ${s.date} • ⏰ ${s.time}</div></div>
                         <div style="display:flex;gap:8px;align-items:center"><button class="btn small ghost" data-id="${s.id}" data-action="view">View</button><button class="btn small ghost" data-id="${s.id}" data-action="edit">Edit</button></div>`;
        sessionsList.appendChild(row);
      });
      // actions
      sessionsList.querySelectorAll('button').forEach(b=>b.addEventListener('click', ()=>{
        const id = Number(b.dataset.id); const act=b.dataset.action;
        const s = state.sessions.find(x=>x.id===id);
        if(act==='view') alert(JSON.stringify(s,null,2));
        if(act==='edit'){ openScheduleModal(s.patientId, s); }
      }));
    }

    // Notifications
    function renderNotifications(){
      notificationsList.innerHTML = '';
      state.notifications.forEach(n=>{
        const div = document.createElement('div'); div.className='list-item';
        div.innerHTML = `<div style="display:flex;justify-content:space-between"><strong>${n.to}</strong><div class="muted">${n.channel} • <span style="color:green">${n.status}</span></div></div>
                         <div style="margin-top:8px">${n.message}</div>
                         <div class="muted" style="margin-top:8px">Type: ${n.type||'general'} • Sent: ${n.sent||''}</div>`;
        notificationsList.appendChild(div);
      });
    }

    // Feedback
    function renderFeedback(){
      feedbackList.innerHTML = '';
      state.feedback.forEach(f=>{
        const div = document.createElement('div'); div.className='list-item';
        div.innerHTML = `<div style="display:flex;justify-content:space-between"><strong>${f.patient}</strong><div class="muted">Session ${f.session} • ${f.date}</div></div>
                         <div style="margin-top:8px"><strong>Pain:</strong> ${f.pain}/10 &nbsp;&nbsp; <strong>Energy:</strong> ${f.energy}/10 &nbsp;&nbsp; <strong>Satisfaction:</strong> ${f.satisfaction}/5</div>
                         <div style="margin-top:8px">${f.notes || ''}</div>`;
        feedbackList.appendChild(div);
      });
    }

    // Activities
    function renderActivities(){
      recentActivities.innerHTML='';
      state.activities.slice(0,6).forEach(a=>{ const d=document.createElement('div'); d.className='list-item'; d.textContent=a; recentActivities.appendChild(d); });
    }

    // Metrics
    function renderMetrics(){
      mPatients.textContent = state.patients.length;
      mActive.textContent = state.patients.filter(p=>p.status==='active').length;
      const today = new Date().toISOString().slice(0,10);
      mSessions.textContent = state.sessions.filter(s=>s.date===today).length;
      mAlerts.textContent = state.notifications.filter(n=>n.status!=='delivered').length;

      document.getElementById('m-sessions').textContent = state.sessions.filter(s=>s.date===today).length;
      document.getElementById('totalFeedback').textContent = state.feedback.length;
      // avg satisfaction
      const avg = state.feedback.length? (state.feedback.reduce((a,b)=>a+b.satisfaction,0)/state.feedback.length).toFixed(1) : 0;
      document.getElementById('avgSatisfaction').textContent = avg;

      // progress metrics
      const completed = state.sessions.filter(s=>s.status==='completed').length;
      const total = state.sessions.length || 1;
      document.getElementById('completedSessions').textContent = `${completed}/${total}`;
      document.getElementById('improvement').textContent = Math.round((completed/total)*100) + '%';
      const next = state.sessions.find(s=>s.status==='scheduled');
      document.getElementById('nextSession').textContent = next? next.date : '-';
      document.getElementById('therapyType').textContent = next? next.type : '-';
    }

    // Chart drawing (simple line chart)
    function drawChart(){
      const labels = ['Week1','Week2','Week3','Week4','Week5','Week6'];
      // sample series from feedback/sessions - generate simple numbers
      const series = labels.map((_,i)=> 30 + i*8 + Math.floor(Math.random()*8));
      const w = chartCanvas.width; const h = chartCanvas.height; ctx.clearRect(0,0,w,h);
      // grid
      ctx.strokeStyle = '#eee'; ctx.lineWidth=1;
      for(let i=0;i<5;i++){ ctx.beginPath(); ctx.moveTo(40, 40 + i*(h-80)/4); ctx.lineTo(w-20, 40 + i*(h-80)/4); ctx.stroke(); }
      // axis labels
      ctx.fillStyle = '#666'; ctx.font='12px Arial'; labels.forEach((l,i)=>{ ctx.fillText(l, 60 + i*(w-120)/(labels.length-1), h-20); });
      // line
      ctx.beginPath(); ctx.strokeStyle = '#28c76f'; ctx.lineWidth=3; series.forEach((v,i)=>{ const x = 40 + i*(w-120)/(series.length-1); const y = (h-40) - (v/100)*(h-100); if(i===0) ctx.moveTo(x,y); else ctx.lineTo(x,y); }); ctx.stroke();
      // points
      ctx.fillStyle='#fff'; ctx.strokeStyle='#28c76f'; series.forEach((v,i)=>{ const x=40 + i*(w-120)/(series.length-1); const y=(h-40)-(v/100)*(h-100); ctx.beginPath(); ctx.arc(x,y,4,0,Math.PI*2); ctx.fill(); ctx.stroke(); });
    }

    // Chatbot (demo) - simple echo with canned suggestions
    function addChatMessage(text, from='ai'){
      const el = document.createElement('div'); el.style.padding='8px'; el.style.borderRadius='8px'; el.style.maxWidth='85%'; el.style.alignSelf = from==='ai'?'flex-start':'flex-end';
      el.style.background = from==='ai'?'#f3fff7':'#e6f7ff'; el.textContent = text; chatLog.appendChild(el); chatLog.scrollTop = chatLog.scrollHeight;
    }

    sendChatBtn.addEventListener('click', ()=>{
      const t = chatInput.value.trim(); if(!t) return; addChatMessage(t,'user'); chatInput.value='';
      // demo response
      setTimeout(()=> addChatMessage('AI Suggestion: ' + (t.includes('schedule') ? 'Suggested slot Dec 2, 11:00' : 'I can help summarize notes and draft reports.')), 600);
    });

    // Reports
    function populateReportPatients(){ reportPatient.innerHTML=''; state.patients.forEach(p=>{ const opt=document.createElement('option'); opt.value=p.id; opt.textContent=p.name; reportPatient.appendChild(opt); }); }

    generateReportBtn.addEventListener('click', ()=>{
      const pid = Number(reportPatient.value); const patient = state.patients.find(p=>p.id===pid);
      const type = document.getElementById('reportType').value; const obs = document.getElementById('reportObservations').value;
      const doc = `Report - ${type}\nPatient: ${patient.name}\nTherapy: ${patient.therapy}\nDate: ${new Date().toLocaleDateString()}\n\nObservations:\n${obs}\n\n(Generated by AyurSutra demo)`;
      // show as download
      const blob = new Blob([doc], {type:'text/plain'}); const url = URL.createObjectURL(blob);
      downloadReportBtn.onclick = ()=>{ const a=document.createElement('a'); a.href=url; a.download = `${patient.name.replace(/\s+/g,'_')}_${type.replace(/\s+/g,'_')}.txt`; a.click(); };
      alert('Report generated. Click Download to save file.');
    });

    // Modal handlers
    addPatientBtn.addEventListener('click', ()=>{ openPatientModal(); });
    closeModalBtn.addEventListener('click', ()=>{ modal.style.display='none'; });

    function openPatientModal(id=null){
      document.getElementById('modalTitle').textContent = id? 'Edit Patient' : 'Add Patient';
      modal.style.display='flex';
      if(id){ const p = state.patients.find(x=>x.id===id); document.getElementById('p_name').value=p.name; document.getElementById('p_age').value=p.age; document.getElementById('p_phone').value=p.phone; document.getElementById('p_email').value=p.email; document.getElementById('p_therapy').value=p.therapy; document.getElementById('p_join').value=p.join;} else { document.getElementById('p_name').value=''; document.getElementById('p_age').value=''; document.getElementById('p_phone').value=''; document.getElementById('p_email').value=''; document.getElementById('p_therapy').value='Panchakarma'; document.getElementById('p_join').value=''; }
      savePatientBtn.dataset.edit = id? id : '';
    }

    savePatientBtn.addEventListener('click', ()=>{
      const id = savePatientBtn.dataset.edit ? Number(savePatientBtn.dataset.edit) : null;
      const p = { name: document.getElementById('p_name').value.trim(), age: document.getElementById('p_age').value.trim(), phone: document.getElementById('p_phone').value.trim(), email: document.getElementById('p_email').value.trim(), therapy: document.getElementById('p_therapy').value, join: document.getElementById('p_join').value, status:'active', lastVisit:'' };
      if(!p.name){ alert('Please provide name'); return; }
      if(id){ const idx = state.patients.findIndex(x=>x.id===id); state.patients[idx] = {...state.patients[idx], ...p}; state.activities.unshift('Patient updated: '+p.name);} else { p.id = uid(); state.patients.unshift(p); state.activities.unshift('New patient registered: '+p.name); }
      modal.style.display='none'; saveData();
    });

    // Scheduling modal
    openScheduleBtn.addEventListener('click', ()=> openScheduleModal());
    closeScheduleBtn.addEventListener('click', ()=> modalSchedule.style.display='none');

    function openScheduleModal(patientId=null, session=null){
      modalSchedule.style.display='flex';
      // populate patients
      const sPatient = document.getElementById('s_patient'); sPatient.innerHTML=''; state.patients.forEach(p=>{ const o=document.createElement('option'); o.value=p.id; o.textContent=p.name; sPatient.appendChild(o); });
      if(patientId) sPatient.value = patientId;
      if(session){ document.getElementById('s_type').value = session.type; document.getElementById('s_date').value = session.date; document.getElementById('s_time').value = session.time; saveScheduleBtn.dataset.edit = session.id; } else { document.getElementById('s_type').value='Abhyanga'; document.getElementById('s_date').value=''; document.getElementById('s_time').value=''; saveScheduleBtn.dataset.edit=''; }
    }

    saveScheduleBtn.addEventListener('click', ()=>{
      const pid = Number(document.getElementById('s_patient').value); const p = state.patients.find(x=>x.id===pid);
      const type = document.getElementById('s_type').value; const date = document.getElementById('s_date').value; const time = document.getElementById('s_time').value;
      if(!p||!date||!time){ alert('Please fill all fields'); return; }
      if(saveScheduleBtn.dataset.edit){ const id = Number(saveScheduleBtn.dataset.edit); const idx = state.sessions.findIndex(x=>x.id===id); state.sessions[idx] = {...state.sessions[idx], type,date,time}; state.activities.unshift('Session updated for '+p.name);} else { const id = uid(); state.sessions.unshift({id, patientId:pid, patientName:p.name, date, time, type, status:'scheduled', practitioner:'Dr. Priya Sharma'}); state.activities.unshift('Session scheduled: '+p.name+' • '+type); }
      modalSchedule.style.display='none'; saveData();
    });

    // Quick actions
    document.getElementById('quickSchedule').addEventListener('click', ()=>{ openScheduleModal(); tabs.forEach(t=>t.classList.remove('active')); document.querySelector('[data-tab="scheduling"]').classList.add('active'); views.forEach(v=>v.style.display = v.id==='scheduling' ? 'block' : 'none'); });
    document.getElementById('quickNotify').addEventListener('click', ()=>{ const msg = prompt('Notification message:'); if(msg){ state.notifications.unshift({id:uid(), to:'All Patients', channel:'SMS', status:'scheduled', message:msg, sent:new Date().toISOString()}); state.activities.unshift('Notification scheduled'); saveData(); }});

    // Send Notification sample
    document.getElementById('sendNotification').addEventListener('click', ()=>{ const to = prompt('Send to (name/email/all):','All Patients'); const message = prompt('Message:','Reminder for your therapy tomorrow'); if(to && message){ state.notifications.unshift({id:uid(), to, channel:'EMAIL', status:'delivered', message, sent:new Date().toLocaleString(), type:'reminder'}); state.activities.unshift('Notification sent to '+to); saveData(); }});

    // recent render
    function renderAll(){ renderPatients(); renderMiniList(); renderSessions(); renderNotifications(); renderFeedback(); renderActivities(); renderMetrics(); populateReportPatients(); drawChart(); }

    // initialize
    renderAll();

    // expose save for debugging
    window._ayursutra = {state, saveData};

  </script>
</body>
</html>

