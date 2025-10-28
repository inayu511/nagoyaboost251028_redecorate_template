<script>
  import { gridState } from '$lib/stores/gridState';

  // 最大値（0..12）
  const MAX_BUTTON_VALUE = 12;

  // 値 → 表示ラベル
  const LABELS = [
    '-',                 // 0
    'チェア↑',           // 1
    'チェア→',           // 2
    'チェア↓',           // 3
    'チェア←',           // 4
    'テーブル↑',         // 5
    'テーブル→',         // 6
    'テーブル↓',         // 7
    'テーブル←',         // 8
    'クローゼット↑',     // 9
    'クローゼット→',     // 10
    'クローゼット↓',     // 11
    'クローゼット←'      // 12
  ];

  // ボタンクリックで state を安全に更新（0..12 を循環）
  function handleClick(index) {
    gridState.update(state => {
      const next = state.slice();
      if (index < 0 || index >= next.length) return next;
      next[index] = next[index] >= MAX_BUTTON_VALUE ? 0 : next[index] + 1;
      return next;
    });
  }

  // 値に応じた CSS クラスを返す（色分け）
  function classFor(value) {
    if (value >= 1 && value <= 4) return 'chair';
    if (value >= 5 && value <= 8) return 'table';
    if (value >= 9 && value <= 12) return 'closet';
    return 'empty';
  }

  // ラベル取得
  function labelFor(value) {
    return LABELS[value] ?? '-';
  }
</script>

<div class="page">
  <div class="left-panel">
    <p class="panel-title">左パネルです</p>
    <!-- 左パネルはここに必要な情報を置けます（画像のように空白でも OK） -->
  </div>

  <div class="right-panel">
    <div class="grid-wrapper" role="grid" aria-label="8x8 grid">
      {#each Array(8) as _, row}
        <div class="grid-row" role="row">
          {#each Array(8) as _, col}
            { /* インデックス計算 */ }
            {#let idx = row * 8 + col}
            <div class="cell" role="gridcell">
              <button
                class="cell-button {classFor($gridState[idx])}"
                on:click={() => handleClick(idx)}
                aria-label={`cell-${idx}`}
                title={labelFor($gridState[idx])}
              >
                {labelFor($gridState[idx])}
              </button>
            </div>
          {/let}
          {/each}
        </div>
      {/each}
    </div>
  </div>
</div>

<style>
  /* レイアウト：左パネル（広め）・右パネル（グリッド） */
  .page {
    display: flex;
    gap: 24px;
    align-items: flex-start;
    padding: 16px;
    font-family: system-ui, -apple-system, "Hiragino Kaku Gothic ProN", "Yu Gothic", "Meiryo", sans-serif;
  }

  .left-panel {
    flex: 1 1 60%;
    min-height: 480px; /* 見た目を合わせるため適宜調整 */
    border-radius: 6px;
    padding: 12px;
    box-sizing: border-box;
    color: #333;
  }

  .panel-title {
    margin: 0 0 8px 0;
    font-size: 13px;
  }

  .right-panel {
    flex: 0 0 auto;
    /* グリッド全体の幅はセルに合わせて自動 */
  }

  /* グリッドを縦に積む（行ごと） */
  .grid-wrapper {
    display: flex;
    flex-direction: column;
    gap: 6px;
    background: transparent;
  }

  .grid-row {
    display: flex;
    gap: 6px;
  }

  .cell {
    width: 56px;
    height: 56px;
  }

  /* ボタンをセルいっぱいにして、中身中央寄せ（文字数に依存しない） */
  .cell-button {
    width: 100%;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    box-sizing: border-box;
    padding: 4px;
    border: 1px solid #e1e1e1;
    border-radius: 4px;
    font-size: 12px;
    cursor: pointer;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    transition: transform 0.08s ease, box-shadow 0.08s ease;
    background-color: #ffffff;
    color: #222;
  }

  .cell-button:active {
    transform: translateY(1px);
  }

  /* ホバー効果 */
  .cell-button:hover {
    box-shadow: 0 1px 2px rgba(0,0,0,0.06);
  }

  /* 色分け：テーブル（赤系）、クローゼット（緑系）、チェア（青系）、空は薄グレー枠 */
  .cell-button.table {
    background-color: #fdecea; /* 薄い赤 */
    border-color: #f5c2c0;
    color: #7a1b1b;
  }

  .cell-button.chair {
    background-color: #eaf3ff; /* 薄い青 */
    border-color: #c9e0ff;
    color: #1f4f8b;
  }

  .cell-button.closet {
    background-color: #e9f8ea; /* 薄い緑 */
    border-color: #c7eac7;
    color: #1f6f2f;
  }

  .cell-button.empty {
    background-color: #fff;
    border-color: #eee;
    color: #777;
  }

  /* 細かい調整：長いラベルでもはみ出さないよう省略表示 */
  .cell-button {
    text-align: center;
    line-height: 1.1;
  }

  /* レスポンシブ：画面が狭いと右パネルを縮める */
  @media (max-width: 720px) {
    .page {
      flex-direction: column;
    }
    .left-panel {
      min-height: 200px;
    }
    .cell {
      width: 40px;
      height: 40px;
    }
    .cell-button {
      font-size: 11px;
    }
  }
</style>
