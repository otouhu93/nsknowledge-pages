---
layout: report
title: Bookmarks
---

<h1>ブックマーク</h1>

<div class="bm-export-bar">
  <button onclick="exportBookmarks()">JSON エクスポート</button>
</div>

<div class="bm-page-folders" id="bm-folders"></div>
<div id="bm-list"></div>

<script>
function renderBookmarksPage(filterFolder) {
  const all = BM.getAll();
  const folders = ['すべて', ...BM.folders()];

  // Folder tabs
  document.getElementById('bm-folders').innerHTML = folders.map(f =>
    '<button class="bm-folder-tab' + (f === (filterFolder || 'すべて') ? ' active' : '') + '" onclick="renderBookmarksPage(\'' + f.replace(/'/g, "\\'") + '\')">' + f +
    ' (' + (f === 'すべて' ? all.length : all.filter(b => b.folder === f).length) + ')</button>'
  ).join('');

  const filtered = (!filterFolder || filterFolder === 'すべて') ? all : all.filter(b => b.folder === filterFolder);

  if (filtered.length === 0) {
    document.getElementById('bm-list').innerHTML = '<div style="text-align:center;color:var(--text-muted);padding:2rem">ブックマークはまだありません。<br>レポート内の ☆ をタップして追加できます。</div>';
    return;
  }

  document.getElementById('bm-list').innerHTML = filtered.map(b => `
    <div class="bm-item">
      <div class="bm-item-title"><a href="${b.url}" target="_blank" rel="noopener">${b.title}</a></div>
      <div class="bm-item-meta">${b.source || ''} | ${b.folder} | ${(b.savedAt || '').slice(0, 10)}</div>
      <div class="bm-item-actions">
        <button onclick="moveBookmark('${b.url.replace(/'/g, "\\'")}')">移動</button>
        <button class="bm-del" onclick="removeBookmark('${b.url.replace(/'/g, "\\'")}')">削除</button>
      </div>
    </div>
  `).join('');
}

function removeBookmark(url) {
  BM.remove(url);
  renderBookmarksPage();
}

function moveBookmark(url) {
  const folders = BM.folders();
  if (!folders.includes('未分類')) folders.unshift('未分類');
  const folder = prompt('移動先フォルダ名（既存: ' + folders.join(', ') + '）');
  if (folder && folder.trim()) {
    BM.updateFolder(url, folder.trim());
    renderBookmarksPage();
  }
}

function exportBookmarks() {
  const data = JSON.stringify(BM.getAll(), null, 2);
  const blob = new Blob([data], { type: 'application/json' });
  const a = document.createElement('a');
  a.href = URL.createObjectURL(blob);
  a.download = 'nsknowledge-bookmarks-' + new Date().toISOString().slice(0, 10) + '.json';
  a.click();
}

renderBookmarksPage();
</script>
