<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Создать публикацию</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 40px;
            background: #f6f8fa;
        }
        .publish-btn {
            font-size: 1.2em;
            padding: 10px 30px;
            cursor: pointer;
        }
        .publication-form {
            display: none;
            max-width: 600px;
            margin-top: 30px;
            background: #fff;
            border-radius: 8px;
            padding: 24px 30px 16px 30px;
            box-shadow: 0 2px 10px -2px #ccc;
        }
        .title-input {
            font-weight: bold;
            font-size: 1.5em;
            text-transform: uppercase;
            width: 100%;
            margin-bottom: 10px;
            border: none;
            border-bottom: 2px solid #444;
            outline: none;
        }
        .divider {
            margin: 12px 0 18px 0;
            border: none;
            height: 2px;
            background: #222;
        }
        .content-area {
            width: 100%;
            min-height: 80px;
            margin-bottom: 14px;
            resize: vertical;
            font-size: 1em;
        }
        .photos-row {
            display: flex;
            flex-direction: row;
            gap: 20px;
            margin-bottom: 10px;
            flex-wrap: wrap;
        }
        .photo-block {
            display: flex;
            align-items: flex-start;
            margin-bottom: 8px;
        }
        .photo-block.left {flex-direction: row;}
        .photo-block.right {flex-direction: row-reverse;}
        .photo-img {
            max-width: 180px;
            max-height: 180px;
            object-fit: contain;
            border: 1px solid #aaa;
            border-radius: 4px;
            background: #eee;
        }
        .photo-text {
            max-width: 160px;
            min-width: 80px;
            margin: 0 10px;
        }
    </style>
</head>
<body>
    <button class="publish-btn" id="startBtn">Сделать публикацию</button>
    <form id="publicationForm" class="publication-form">
        <input class="title-input" type="text" id="title" placeholder="TITLE" required>
        <hr class="divider">
        <textarea class="content-area" id="mainText" placeholder="Напишите текст публикации..." required></textarea>
        <div id="photosRow" class="photos-row"></div>
        <input type="file" id="photoInput" accept="image/*" multiple style="margin-bottom:12px;">
        <button type="button" id="addPhotoBtn">Добавить фотографию</button>
        <button type="submit" style="float:right;">Опубликовать</button>
    </form>

    <div id="result"></div>

    <script>
        const startBtn = document.getElementById('startBtn');
        const publicationForm = document.getElementById('publicationForm');
        const photosRow = document.getElementById('photosRow');
        const photoInput = document.getElementById('photoInput');
        const addPhotoBtn = document.getElementById('addPhotoBtn');
        const result = document.getElementById('result');
        let photoCount = 0;

        startBtn.onclick = () => {
            publicationForm.style.display = 'block';
            startBtn.style.display = 'none';
        };

        addPhotoBtn.onclick = () => {
            if (!photoInput.files.length || photoCount >= 3) return;
            for (const file of photoInput.files) {
                if (photoCount >= 3) break;
                const reader = new FileReader();
                reader.onload = function(e) {
                    const photoBlock = document.createElement('div');
                    photoBlock.className = 'photo-block';
                    // Decide left or right: check if textarea cursor is at line start (after Enter)
                    const mainText = document.getElementById('mainText');
                    const textContent = mainText.value;
                    const lines = textContent.split('\n');
                    // If last line is empty (Enter just pressed after adding photo) => photo left, else right
                    if (lines.length > 1 && lines[lines.length-1].trim() === "") {
                        photoBlock.classList.add('left');
                    } else {
                        photoBlock.classList.add('right');
                    }
                    // Photo
                    const img = document.createElement('img');
                    img.className = 'photo-img';
                    img.src = e.target.result;
                    // Text beside photo
                    const photoTxt = document.createElement('textarea');
                    photoTxt.className = 'photo-text';
                    photoTxt.placeholder = "Текст рядом с фото...";
                    photoBlock.appendChild(img);
                    photoBlock.appendChild(photoTxt);
                    photoCount++;
                    photosRow.appendChild(photoBlock);
                };
                reader.readAsDataURL(file);
            }
            // Очищает input для возможности добавлять одни и те же файлы снова
            photoInput.value = "";
        };

        publicationForm.onsubmit = function(e) {
            e.preventDefault();
            // Form result render (demo, no backend)
            let html = `<h2 style="text-transform:uppercase;">${document.getElementById('title').value}</h2>
            <hr style="margin:12px 0;">
            <div style="margin-bottom:10px;white-space:pre-line;">${document.getElementById('mainText').value}</div>`;
            // Photos output
            for (const block of photosRow.children) {
                const imgSrc = block.querySelector('.photo-img').src;
                const textVal = block.querySelector('.photo-text').value;
                html += `<div style="display:flex;align-items:flex-start;margin-bottom:8px;flex-direction:${block.classList.contains('left') ? 'row':'row-reverse'};">
                    <img src="${imgSrc}" style="max-width:180px;max-height:180px;border-radius:4px;background:#eee;border:1px solid #aaa;">
                    <div style="margin:0 10px;max-width:160px;">${textVal}</div>
                </div>`;
            }
            result.innerHTML = `<div style="max-width:650px;background:#fff;margin-top:40px;padding:28px 40px 25px 40px;border-radius:8px;box-shadow:0 2px 10px -2px #ccc;">${html}</div>`;
            publicationForm.reset();
            photosRow.innerHTML = '';
            photoCount = 0;
            publicationForm.style.display = 'none';
            startBtn.style.display = '';
        };
    </script>
</body>
</html>
