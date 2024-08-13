<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Enhanced YouTube & Image Tools</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background: linear-gradient(135deg, #ff6f61, #dce35b);
            margin: 0;
            padding: 20px;
            transition: background-color 0.3s ease;
        }
        .container {
            max-width: 800px;
            margin: 0 auto;
            background: #fff;
            padding: 20px;
            border-radius: 12px;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
        }
        h1 {
            color: #333;
            margin-bottom: 20px;
        }
        .tool-section {
            display: none;
        }
        input[type="text"], select {
            width: 100%;
            padding: 12px;
            margin: 10px 0;
            border: 2px solid #007bff;
            border-radius: 8px;
            box-sizing: border-box;
            font-size: 16px;
        }
        input[type="button"] {
            padding: 12px;
            margin: 10px 0;
            border: none;
            border-radius: 8px;
            background-color: #ff6f61;
            color: #fff;
            cursor: pointer;
            font-size: 16px;
            transition: background-color 0.3s ease, transform 0.2s ease;
        }
        input[type="button"]:hover {
            background-color: #ff3b30;
            transform: scale(1.05);
        }
        input[type="button"]:active {
            background-color: #e73d2d;
        }
        img {
            margin: 20px 0;
            max-width: 100%;
            border: 2px solid #ddd;
            border-radius: 8px;
            display: none;
        }
        a {
            display: block;
            margin: 10px 0;
            padding: 12px;
            color: #fff;
            background-color: #007bff;
            border-radius: 8px;
            text-decoration: none;
            font-size: 16px;
            transition: background-color 0.3s ease, color 0.3s ease;
        }
        a:hover {
            background-color: #0056b3;
            color: #fff;
        }
        a:active {
            background-color: #004080;
            color: #fff;
        }
        .section-header {
            font-size: 22px;
            color: #333;
            margin-bottom: 20px;
        }
        .section {
            margin-bottom: 40px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Enhanced YouTube & Image Tools</h1>
        
        <!-- Tool Selection Buttons -->
        <div>
            <input type="button" value="Thumbnail Downloader" onclick="showSection('thumbnail')">
            <input type="button" value="Image Generator" onclick="showSection('image')">
            <input type="button" value="Video Metadata" onclick="showSection('metadata')">
            <input type="button" value="Image Search" onclick="showSection('search')">
            <input type="button" value="Image Editor" onclick="showSection('editor')">
        </div>

        <!-- Thumbnail Downloader Section -->
        <div id="thumbnailSection" class="tool-section">
            <h2 class="section-header">Thumbnail Downloader</h2>
            <p>Enter the YouTube video URL to view and download the thumbnail.</p>
            <input type="text" id="thumbnailUrl" placeholder="Enter YouTube video URL">
            
            <select id="thumbnailSizeSelect">
                <option value="maxresdefault.jpg">High Quality (Max Resolution)</option>
                <option value="sddefault.jpg">Standard Definition</option>
                <option value="mqdefault.jpg">Medium Quality</option>
                <option value="hqdefault.jpg">High Quality</option>
                <option value="default.jpg">Default Quality</option>
            </select>
            
            <input type="button" value="Show Thumbnail" onclick="showThumbnail()">
            
            <div id="thumbnailResult">
                <img id="thumbnail" src="" alt="YouTube Thumbnail">
                <a id="thumbnailDownloadLink" href="" download="thumbnail.jpg" style="display:none;">Download Thumbnail</a>
            </div>
        </div>

        <!-- Image Generator Section -->
        <div id="imageSection" class="tool-section">
            <h2 class="section-header">Image Generator</h2>
            <p>Enter a keyword to generate and download an image based on the keyword.</p>
            <input type="text" id="keyword" placeholder="Enter a keyword (e.g., bird, flower, nature)">
            <input type="button" value="Generate Image" onclick="generateImage()">
            
            <div id="result">
                <img id="generatedImage" src="" alt="Generated Image">
                <a id="downloadLink" href="" download="generated_image.jpg" style="display:none;">Download Image</a>
            </div>
        </div>

        <!-- Video Metadata Section -->
        <div id="metadataSection" class="tool-section">
            <h2 class="section-header">Video Metadata</h2>
            <p>Enter the YouTube video URL to fetch and display metadata.</p>
            <input type="text" id="metadataUrl" placeholder="Enter YouTube video URL">
            <input type="button" value="Fetch Metadata" onclick="fetchMetadata()">
            
            <div id="metadataResult">
                <pre id="metadataOutput" style="text-align: left; white-space: pre-wrap; display: none;"></pre>
            </div>
        </div>

        <!-- Image Search Section -->
        <div id="searchSection" class="tool-section">
            <h2 class="section-header">Image Search</h2>
            <p>Enter a keyword to search for images.</p>
            <input type="text" id="searchKeyword" placeholder="Enter a keyword (e.g., sunset, mountains)">
            <input type="button" value="Search Images" onclick="searchImages()">
            
            <div id="searchResults">
                <div id="searchImages"></div>
            </div>
        </div>

        <!-- Image Editor Section -->
        <div id="editorSection" class="tool-section">
            <h2 class="section-header">Image Editor</h2>
            <p>Upload an image to apply basic edits.</p>
            <input type="file" id="uploadImage" accept="image/*">
            <input type="button" value="Apply Filter" onclick="applyFilter()">
            
            <div id="editorResult">
                <canvas id="editorCanvas" style="display:none;"></canvas>
                <img id="editorImage" src="" alt="Uploaded Image" style="display:none;">
            </div>
        </div>
    </div>

    <script>
        function showSection(sectionId) {
            // Hide all sections
            document.querySelectorAll('.tool-section').forEach(section => {
                section.style.display = 'none';
            });

            // Show the selected section
            document.getElementById(sectionId + 'Section').style.display = 'block';
        }

        async function showThumbnail() {
            const url = document.getElementById('thumbnailUrl').value;
            const videoId = extractVideoId(url);
            const size = document.getElementById('thumbnailSizeSelect').value;

            if (videoId) {
                const thumbnailUrl = `https://img.youtube.com/vi/${videoId}/${size}`;
                const thumbnailImg = document.getElementById('thumbnail');
                const downloadLink = document.getElementById('thumbnailDownloadLink');

                thumbnailImg.src = thumbnailUrl;
                thumbnailImg.style.display = 'block';
                downloadLink.href = thumbnailUrl;
                downloadLink.style.display = 'block';
                
                // Change background color to sky blue
                document.body.style.backgroundColor = 'skyblue';
            } else {
                alert('Please enter a valid YouTube URL.');
                document.getElementById('thumbnail').style.display = 'none';
                document.getElementById('thumbnailDownloadLink').style.display = 'none';
            }
        }

        async function generateImage() {
            const keyword = document.getElementById('keyword').value;
            const imageUrl = await fetchImageUrl(keyword);

            if (imageUrl) {
                const image = document.getElementById('generatedImage');
                const downloadLink = document.getElementById('downloadLink');

                image.src = imageUrl;
                image.style.display = 'block';
                downloadLink.href = imageUrl;
                downloadLink.style.display = 'block';
                
                // Change background color to sky blue
                document.body.style.backgroundColor = 'skyblue';
            } else {
                alert('Unable to generate image. Please try a different keyword.');
                document.getElementById('generatedImage').style.display = 'none';
                document.getElementById('downloadLink').style.display = 'none';
            }
        }

        async function fetchImageUrl(keyword) {
            // Replace with your Unsplash API key
            const apiKey = 'YOUR_UNSPLASH_API_KEY';
            const response = await fetch(`https://api.unsplash.com/photos/random?query=${encodeURIComponent(keyword)}&client_id=${apiKey}`);
            const data = await response.json();
            return data && data[0] && data[0].urls && data[0].urls.regular;
        }

        function extractVideoId(url) {
            const regex = /(?:https?:\/\/)?(?:www\.)?youtube\.com\/(?:watch\?v=|embed\/|v\/|.+\?v=)?([^"&?\/\s]{11})|(?:https?:\/\/)?(?:www\.)?youtu\.be\/([^"&?\/\s]{11})/;
            const match = url.match(regex);
            return match ? (match[1] || match[2]) : null;
        }

        async function fetchMetadata() {
            const url = document.getElementById('metadataUrl').value;
            const videoId = extractVideoId(url);

            if (videoId) {
                const response = await fetch(`https://www.googleapis.com/youtube/v3/videos?id=${videoId}&key=YOUR_API_KEY&part=snippet,contentDetails,statistics`);
                const data = await response.json();
                const output = JSON.stringify(data, null, 2);
                document.getElementById('metadataOutput').textContent = output;
                document.getElementById('metadataOutput').style.display = 'block';
            } else {
                alert('Please enter a valid YouTube URL.');
                document.getElementById('metadataOutput').style.display = 'none';
            }
        }

        async function searchImages() {
            const keyword = document.getElementById('searchKeyword').value;
            const imagesContainer = document.getElementById('searchImages');

            // Clear previous results
            imagesContainer.innerHTML = '';

            // Replace with your Unsplash API key
            const apiKey = 'YOUR_UNSPLASH_API_KEY';
            const response = await fetch(`https://api.unsplash.com/search/photos?query=${encodeURIComponent(keyword)}&client_id=${apiKey}`);
            const data = await response.json();

            if (data && data.results) {
                data.results.forEach(image => {
                    const img = document.createElement('img');
                    img.src = image.urls.thumb;
                    img.alt = image.alt_description;
                    img.style.margin = '10px';
                    img.style.borderRadius = '8px';
                    img.style.maxWidth = '150px';
                    imagesContainer.appendChild(img);
                });
            } else {
                alert('No images found.');
            }
        }

        function applyFilter() {
            const fileInput = document.getElementById('uploadImage');
            const canvas = document.getElementById('editorCanvas');
            const ctx = canvas.getContext('2d');
            const img = document.getElementById('editorImage');

            if (fileInput.files.length > 0) {
                const file = fileInput.files[0];
                const reader = new FileReader();

                reader.onload = function(e) {
                    img.src = e.target.result;
                    img.onload = function() {
                        canvas.width = img.width;
                        canvas.height = img.height;
                        ctx.drawImage(img, 0, 0);

                        // Apply a basic filter (e.g., grayscale)
                        ctx.globalCompositeOperation = 'saturation';
                        ctx.fillStyle = 'rgba(0, 0, 0, 0.5)';
                        ctx.fillRect(0, 0, canvas.width, canvas.height);

                        canvas.style.display = 'block';
                    };
                };
                reader.readAsDataURL(file);
            } else {
                alert('Please upload an image.');
            }
        }
    </script>
</body>
</html>
