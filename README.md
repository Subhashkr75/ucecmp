<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <title>Universal Aadhaar Training Portal - Subhash Kumar</title>
    <style>
        :root { --primary: #06038d; --secondary: #ff9933; --success: #138808; }
        body { font-family: 'Segoe UI', Tahoma, sans-serif; background: #eef2f3; margin: 0; padding: 20px; }
        .main-card { max-width: 1100px; margin: auto; background: white; border-radius: 12px; box-shadow: 0 10px 30px rgba(0,0,0,0.2); overflow: hidden; border-top: 8px solid var(--primary); }
        .header { background: #fff; padding: 15px 25px; display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid #ddd; }
        .mode-bar { background: #fff3cd; padding: 12px; text-align: center; border-bottom: 1px solid #ffeeba; }
        .grid { display: grid; grid-template-columns: 1.2fr 1fr 0.8fr; gap: 20px; padding: 25px; }
        .section { background: #fafafa; padding: 15px; border: 1px solid #eee; border-radius: 8px; }
        h3 { color: var(--primary); font-size: 16px; margin-top: 0; border-bottom: 2px solid var(--secondary); display: inline-block; }
        label { display: block; font-weight: bold; margin: 12px 0 5px; font-size: 13px; }
        input, select, textarea { width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 4px; box-sizing: border-box; }
        
        /* Media & Bio Styling */
        .preview-box { width: 100%; height: 200px; background: #000; border-radius: 6px; overflow: hidden; position: relative; border: 2px solid #333; }
        video, #captured-img { width: 100%; height: 100%; object-fit: cover; }
        .btn-action { width: 100%; padding: 10px; margin-top: 10px; cursor: pointer; border: none; border-radius: 4px; font-weight: bold; transition: 0.3s; }
        .btn-capture { background: var(--primary); color: white; }
        .btn-bio { background: var(--secondary); color: white; }
        .btn-submit { background: var(--success); color: white; font-size: 20px; padding: 15px; width: 95%; margin: 0 auto 25px; display: block; }
        
        /* Receipt */
        #receipt { display: none; margin: 20px; padding: 30px; border: 2px solid #000; background: #fff; position: relative; }
        .receipt-photo { width: 130px; height: 150px; border: 1px solid #000; float: right; }
        @media print { .no-print { display: none; } #receipt { display: block; border: none; } }
    </style>
</head>
<body>

<div class="main-card no-print">
    <div class="header">
        <h2 style="margin:0; color:var(--primary);">UIDAI Universal Client (Training)</h2>
        <div><strong>Operator:</strong> Subhash Kumar | <strong>Station ID:</strong> 12548</div>
    </div>

    <div class="mode-bar">
        <strong>Service Type:</strong> 
        <input type="radio" name="stype" value="New Enrollment" checked onclick="toggleUpdate(false)"> New Enrollment 
        <input type="radio" name="stype" value="Aadhaar Update" onclick="toggleUpdate(true)"> Aadhaar Update
    </div>

    <div class="grid">
        <div class="section">
            <h3>1. Demographic Details</h3>
            <div id="update-field" style="display:none; background:#d1ecf1; padding:10px; margin-bottom:10px; border-radius:4px;">
                <label>Aadhaar Number to Update</label>
                <input type="text" id="old-uid" placeholder="XXXX XXXX XXXX">
            </div>
            
            <label>Full Name</label>
            <input type="text" id="name" placeholder="Resident Name">
            
            <div style="display:flex; gap:10px;">
                <div style="flex:1;"><label>Gender</label><select id="gender"><option>Male</option><option>Female</option><option>Other</option></select></div>
                <div style="flex:1;"><label>DOB</label><input type="date" id="dob"></div>
            </div>

            <label>C/O (Care of)</label>
            <input type="text" id="co" placeholder="Father/Husband Name">
            
            <label>Address (House/Street/Village)</label>
            <textarea id="addr" rows="2"></textarea>
            
            <div style="display:flex; gap:10px;">
                <div style="flex:1;"><label>Pincode</label><input type="number" id="pin"></div>
                <div style="flex:1;"><label>District</label><input type="text" id="dist" value="East Champaran"></div>
            </div>
        </div>

        <div class="section">
            <h3>2. Documents (POI/POA/DOB)</h3>
            <label>Identity Proof (POI)</label>
            <select id="doc-poi">
                <option>PAN Card</option>
                <option>Voter ID</option>
                <option>Passport</option>
            </select>
            
            <label>Address/DOB Proof</label>
            <select id="doc-poa">
                <option>Birth Certificate (Janam Praman)</option>
                <option>Residence Certificate (Niwas)</option>
                <option>Ration Card</option>
                <option>School LC/Marksheet</option>
            </select>

            <label style="margin-top:20px;">Manual Scan / Upload Document</label>
            <input type="file" id="docFile" multiple>
            <p style="font-size:11px; color:#666;">*Training mode: Files are not stored.</p>
        </div>

        <div class="section">
            <h3>3. Biometric Capture</h3>
            <div class="preview-box">
                <video id="video" autoplay playsinline></video>
                <img id="captured-img" style="display:none;">
            </div>
            <button class="btn-action btn-capture" onclick="takePhoto()">Capture Photo</button>
            <button class="btn-action" style="background:#6c757d; color:white;" onclick="document.getElementById('photoUpload').click()">Upload Photo</button>
            <input type="file" id="photoUpload" style="display:none;" accept="image/*" onchange="uploadPhoto(event)">

            <label style="margin-top:15px;">Mantra L1 Fingerprint</label>
            <button class="btn-action btn-bio" onclick="captureMantra()">Start Mantra Scan</button>
            <div id="bio-status" style="font-size:12px; margin-top:5px; text-align:center;">Device Status: <span id="status-text">Ready</span></div>
        </div>
    </div>

    <button class="btn-submit" onclick="generateReceipt()">Validate & Final Submission</button>
</div>

<div id="receipt">
    <div style="border-bottom: 2px solid #000; padding-bottom: 10px; margin-bottom: 20px; display: flex; justify-content: space-between;">
        <h2 style="margin:0;">Aadhaar Acknowledgement</h2>
        <div style="text-align:right;"><strong>Training Copy</strong></div>
    </div>
    
    <img id="r-photo" class="receipt-photo">
    
    <p><strong>Enrollment ID (EID):</strong> <span id="r-eid"></span></p>
    <p><strong>Service Type:</strong> <span id="r-type"></span></p>
    <p><strong>Resident Name:</strong> <span id="r-name"></span></p>
    <p><strong>C/O:</strong> <span id="r-co"></span></p>
    <p><strong>Address:</strong> <span id="r-addr"></span></p>
    <p><strong>Pincode:</strong> <span id="r-pin"></span></p>
    <p><strong>Biometric Status:</strong> Captured (Mantra L1)</p>
    
    <div style="margin-top:50px; border-top:1px solid #ccc; padding-top:10px; font-size:12px;">
        <div style="display:flex; justify-content:space-between;">
            <span>Operator: Subhash Kumar</span>
            <span>Date: <span id="r-date"></span></span>
        </div>
    </div>
    <center><button class="no-print" onclick="window.print()" style="margin-top:20px; padding:10px;">Print Acknowledgement</button></center>
</div>

<script>
    const video = document.getElementById('video');
    const capImg = document.getElementById('captured-img');
    const canvas = document.createElement('canvas');

    // Camera Access
    navigator.mediaDevices.getUserMedia({ video: true })
        .then(s => { video.srcObject = s; })
        .catch(e => { console.log("Camera Blocked"); });

    function toggleUpdate(val) { document.getElementById('update-field').style.display = val ? 'block' : 'none'; }

    function takePhoto() {
        canvas.width = video.videoWidth; canvas.height = video.videoHeight;
        canvas.getContext('2d').drawImage(video, 0, 0);
        showPhoto(canvas.toDataURL('image/png'));
    }

    function uploadPhoto(e) {
        const reader = new FileReader();
        reader.onload = () => showPhoto(reader.result);
        reader.readAsDataURL(e.target.files[0]);
    }

    function showPhoto(src) {
        video.style.display = 'none';
        capImg.src = src;
        capImg.style.display = 'block';
    }

    // MANTRA L1 INTEGRATION
    function captureMantra() {
        document.getElementById('status-text').innerText = "Light is ON...";
        document.getElementById('status-text').style.color = "blue";
        
        const mantraUrl = "http://127.0.0.1:11100/rd/capture";
        const xml = '<?xml version="1.0"?> <Opts fCount="1" fType="0" iCount="0" iType="0" pCount="0" pType="0" format="0" pidVer="2.0" timeout="10000" posh="UNKNOWN" env="P" /> ';

        fetch(mantraUrl, { method: "CAPTURE", body: xml })
        .then(res => res.text())
        .then(data => {
            if(data.includes('errCode="0"')) {
                document.getElementById('status-text').innerText = "Fingerprint Success!";
                document.getElementById('status-text').style.color = "green";
            } else {
                document.getElementById('status-text').innerText = "Capture Failed / Timeout";
                document.getElementById('status-text').style.color = "red";
            }
        })
        .catch(() => alert("RD Service not found! Please start Mantra RD Service."));
    }

    function generateReceipt() {
        const name = document.getElementById('name').value;
        if(!name || !capImg.src) return alert("Pehle Name aur Photo confirm karein!");

        document.getElementById('r-eid').innerText = "2026/" + Math.floor(10000+Math.random()*90000) + "/" + Math.floor(10000+Math.random()*90000);
        document.getElementById('r-type').innerText = document.querySelector('input[name="stype"]:checked').value;
        document.getElementById('r-name').innerText = name;
        document.getElementById('r-co').innerText = document.getElementById('co').value;
        document.getElementById('r-addr').innerText = document.getElementById('addr').value;
        document.getElementById('r-pin').innerText = document.getElementById('pin').value;
        document.getElementById('r-photo').src = capImg.src;
        document.getElementById('r-date').innerText = new Date().toLocaleString();
        
        document.getElementById('receipt').style.display = 'block';
        window.scrollTo(0, document.body.scrollHeight);
    }
</script>

</body>
</html>
