<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Ventr Prototype</title>
  <style>
    body { font-family: sans-serif; margin: 0; padding: 0; background: #f0f2f5; }
    header { background: #4b79a1; color: white; padding: 1rem; text-align: center; }
    .container { max-width: 480px; margin: 2rem auto; background: white; padding: 1rem; border-radius: 8px; }
    button { background: #4b79a1; color: white; padding: .75rem 1.5rem; border: none; border-radius: 4px; cursor: pointer; }
    input, select, textarea { width: 100%; margin: .5rem 0; padding: .5rem; border-radius: 4px; border: 1px solid #ccc; }
    .hidden { display: none; }
    .mode-select { margin: 1rem 0; }
    .chat-window { border: 1px solid #ccc; padding: .75rem; height: 240px; overflow-y: auto; background: #fafafa; }
    .chat-bubble { padding: .5rem; margin: .25rem 0; border-radius: 4px; }
    .user { background: #d1e7dd; text-align: right; }
    .ai { background: #f8d7da; text-align: left; }
  </style>
</head>
<body>
  <header><h1>Ventr</h1><p>Safe space to vent your anger</p></header>
  <div class="container" id="step-start">
    <button id="btn-start">Start Venting</button>
  </div>

  <div class="container hidden" id="step-setup">
    <h2>Pick a Persona</h2>
    <select id="persona-type">
      <option>Boss</option><option>Parent</option><option>Friend</option><option>Ex</option><option>Custom</option>
    </select>
    <input id="persona-name" placeholder="Name (e.g. Alex)" />
    <input id="persona-traits" placeholder="Traits (e.g. strict, passive)" />
    <button id="btn-setup-next">Next</button>
  </div>

  <div class="container hidden" id="step-vent">
    <h2>Venting at <span id="target-name"></span></h2>
    <div class="mode-select">
      <label><input type="radio" name="mode" value="silent" checked /> Silent Listener</label><br>
      <label><input type="radio" name="mode" value="echo" /> Echo Mode</label><br>
      <label><input type="radio" name="mode" value="simulate" /> Simulated Response</label><br>
      <label><input type="radio" name="mode" value="therapist" /> Therapist Reflection</label>
    </div>
    <div class="chat-window" id="chat-window"></div>
    <textarea id="user-input" placeholder="Type your vent..."></textarea>
    <button id="btn-send">Send</button>
  </div>

  <div class="container hidden" id="step-end">
    <h2>It’s Over – How Do You Feel?</h2>
    <select id="mood">
      <option>Relieved</option><option>Still Angry</option><option>Calmer</option><option>Sad</option>
    </select>
    <textarea id="journal" placeholder="Optional: Journal about how you're feeling..."></textarea>
    <button id="btn-done">Finish</button>
  </div>

  <script>
    const $ = id => document.getElementById(id)
    $('btn-start').onclick = () => {
      $('step-start').classList.add('hidden')
      $('step-setup').classList.remove('hidden')
    }
    $('btn-setup-next').onclick = () => {
      const name = $('persona-name').value || $('persona-type').value
      $('target-name').innerText = name
      $('step-setup').classList.add('hidden')
      $('step-vent').classList.remove('hidden')
    }
    $('btn-send').onclick = () => {
      const text = $('user-input').value.trim()
      if (!text) return
      const mode = document.querySelector('input[name="mode"]:checked').value
      addBubble(text, 'user')
      const reply = generateReply(text, mode)
      setTimeout(() => addBubble(reply, mode === 'silent' ? 'ai' : 'ai'), 500)
      $('user-input').value = ''
    }
    function addBubble(msg, cls) {
      const div = document.createElement('div')
      div.className = 'chat-bubble ' + cls
      div.innerText = msg
      $('chat-window').append(div)
      $('chat-window').scrollTop = $('chat-window').scrollHeight
    }
    function generateReply(text, mode) {
      switch (mode) {
        case 'silent': return '...'
        case 'echo': return `I hear you: "${text}" 😐`
        case 'simulate': return `("${$('target-name').innerText}" says:) I'm surprised you feel that way.`
        case 'therapist': return `Sounds like you’re really ${extractEmotion(text)}. That must be tough.`
      }
    }
    function extractEmotion(t) {
      if (t.match(/angry|mad/)) return 'angry'
      if (t.match(/sad|upset/)) return 'upset'
      return 'frustrated'
    }
    $('btn-done').onclick = () => {
      alert('Thank you for sharing—take care!')
      location.reload()
    }
  </script>
</body>
</html>
