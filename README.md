<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Pentest Notes - Hacker's Notebook</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Fira+Mono:wght@400;700&display=swap');
    body {
      background: linear-gradient(135deg, #0f2027 0%, #2c5364 100%);
      color: #f8f8f2;
      font-family: 'Fira Mono', monospace;
      margin: 0;
      padding: 0;
      min-height: 100vh;
    }
    h1, h2, h3 {
      color: #ff79c6;
      letter-spacing: 1px;
      text-shadow: 0 2px 8px #23294688;
    }
    a {
      color: #8be9fd;
      text-decoration: none;
      transition: color 0.2s;
    }
    a:hover {
      color: #50fa7b;
      text-decoration: underline;
    }
    .container {
      max-width: 900px;
      margin: 48px auto;
      background: #1a1a2e;
      padding: 40px 36px;
      border-radius: 16px;
      box-shadow: 0 8px 32px rgba(0,0,0,0.6);
      border: 1.5px solid #232946;
    }
    code, pre {
      background: #232946;
      color: #f8f8f2;
      border-radius: 8px;
      padding: 8px 12px;
      font-size: 1.05em;
      display: block;
      margin: 16px 0;
      overflow-x: auto;
    }
    .section {
      margin-bottom: 32px;
    }
    .note-list {
      list-style: none;
      padding: 0;
      margin: 0 0 24px 0;
    }
    .note-list li {
      background: #232946;
      margin-bottom: 10px;
      padding: 10px 16px;
      border-radius: 6px;
      color: #f1fa8c;
      font-size: 1em;
      display: flex;
      align-items: center;
      justify-content: space-between;
    }
    .note-list li .delete-btn {
      background: none;
      border: none;
      color: #ff5555;
      font-size: 1.1em;
      cursor: pointer;
      margin-left: 12px;
      transition: color 0.2s;
    }
    .note-list li .delete-btn:hover {
      color: #ff79c6;
    }
    .add-note-form {
      display: flex;
      gap: 12px;
      margin-bottom: 24px;
    }
    .add-note-form input, .add-note-form textarea {
      background: #232946;
      color: #f8f8f2;
      border: 1px solid #44475a;
      border-radius: 6px;
      padding: 8px;
      font-family: inherit;
      font-size: 1em;
      outline: none;
      transition: border 0.2s;
    }
    .add-note-form input:focus, .add-note-form textarea:focus {
      border: 1.5px solid #8be9fd;
    }
    .add-note-form button {
      background: #50fa7b;
      color: #232946;
      border: none;
      border-radius: 6px;
      padding: 8px 18px;
      font-size: 1em;
      font-weight: bold;
      cursor: pointer;
      transition: background 0.2s, color 0.2s;
    }
    .add-note-form button:hover {
      background: #8be9fd;
      color: #1a1a2e;
    }
    .footer {
      text-align: center;
      margin-top: 40px;
      color: #6272a4;
      font-size: 0.95em;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>🔥 Pentest Notes - Hacker's Notebook</h1>
    <p>Welcome, operator. This is your <strong>powerful</strong> pentest notebook. Log findings, commands, payloads, and tips. Your arsenal, your way.</p>
    
    <div class="section">
      <h2>Quick Commands</h2>
      <pre><code>nmap -A -T4 target.com
gobuster dir -u http://target.com -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
wfuzz -c -w wordlist.txt --hc 404 http://target.com/FUZZ
</code></pre>
    </div>
    
    <div class="section">
      <h2>Notes</h2>
      <form class="add-note-form" id="addNoteForm">
        <input type="text" id="noteTitle" placeholder="Title" required>
        <textarea id="noteContent" placeholder="Your note..." rows="1" required></textarea>
        <button type="submit">Add</button>
      </form>
      <ul class="note-list" id="noteList"></ul>
    </div>
    
    <div class="section">
      <h2>Tips</h2>
      <ul>
        <li>Use <code>tmux</code> or <code>screen</code> for multitasking.</li>
        <li>Keep payloads and wordlists handy.</li>
        <li>Document every step for reproducibility.</li>
        <li>Encrypt sensitive notes.</li>
      </ul>
    </div>
    
    <div class="footer">
      &copy; 2024 Hacker's Notebook &mdash; Stay sharp, stay safe.
    </div>
  </div>
  <script>
    // Simple localStorage-powered notes
    const noteList = document.getElementById('noteList');
    const addNoteForm = document.getElementById('addNoteForm');
    const noteTitle = document.getElementById('noteTitle');
    const noteContent = document.getElementById('noteContent');

    function loadNotes() {
      noteList.innerHTML = '';
      const notes = JSON.parse(localStorage.getItem('pentestNotes') || '[]');
      notes.forEach((note, idx) => {
        const li = document.createElement('li');
        li.innerHTML = `<strong>${note.title}</strong>: ${note.content}
          <button class="delete-btn" title="Delete" onclick="deleteNote(${idx})">&#10006;</button>`;
        noteList.appendChild(li);
      });
    }

    function saveNote(title, content) {
      const notes = JSON.parse(localStorage.getItem('pentestNotes') || '[]');
      notes.unshift({ title, content });
      localStorage.setItem('pentestNotes', JSON.stringify(notes));
      loadNotes();
    }

    function deleteNote(idx) {
      const notes = JSON.parse(localStorage.getItem('pentestNotes') || '[]');
      notes.splice(idx, 1);
      localStorage.setItem('pentestNotes', JSON.stringify(notes));
      loadNotes();
    }

    addNoteForm.addEventListener('submit', function(e) {
      e.preventDefault();
      if (noteTitle.value.trim() && noteContent.value.trim()) {
        saveNote(noteTitle.value.trim(), noteContent.value.trim());
        noteTitle.value = '';
        noteContent.value = '';
      }
    });

    // Expose deleteNote globally
    window.deleteNote = deleteNote;

    // Load notes on page load
    loadNotes();
  </script>
</body>
</html>
