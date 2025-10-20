## How to use
Press F12 on your browser
Go to console tab

Copy and paste the following javascript codes

## Repost Removal Tool

Removes all reposts you made.
Run this code while in: https://x.com/YOURPROFILE

```javascript
// Mass retweet removal — seletor data-testid="unretweetConfirm"
// Paste on Browser Console (F12) and press enter

(async () => {
  const delay = ms => new Promise(r => setTimeout(r, ms));
  const clickDelay = 800;
  const popupDelay = 600;
  const limit = 300;

  function findRetweetButtons() {
    return Array.from(document.querySelectorAll('button[data-testid="unretweet"]'))
      .filter(b => b.offsetParent !== null);
  }

  async function confirmUnretweet() {
    for (let i = 0; i < 15; i++) {
      const confirmBtn = document.querySelector('[data-testid="unretweetConfirm"]');
      if (confirmBtn && confirmBtn.offsetParent !== null) {
        confirmBtn.click();
        return true;
      }
      await delay(100);
    }
    return false;
  }

  let count = 0;
  while (count < limit) {
    const btns = findRetweetButtons();
    if (btns.length === 0) break;

    const btn = btns[0];
    btn.scrollIntoView({block: 'center'});
    btn.click();
    await delay(popupDelay);
    await confirmUnretweet();
    count++;
    await delay(clickDelay);
  }

  console.log(`Retuítes removidos: ${count}`);
})();
```

## Post / Replies Removal Tool

Removes all posts or replies you made.
Run this code while in: https://x.com/YOURPROFILE or/and https://x.com/YOURPROFILE/with_replies

```javascript
// Mass post/replies removal
// Paste on Browser Console (F12) and press enter

(async () => {
  const delay = ms => new Promise(r => setTimeout(r, ms));
  const clickDelay = 250;
  const menuDelay = 100;
  const maxScrollAttempts = 5;

  function extractHandleFromURL() {
    const pathParts = window.location.pathname.split('/').filter(Boolean);
    return pathParts[0];
  }

  function findUserReplyCells(userHandle) {
    return Array.from(document.querySelectorAll('[data-testid="cellInnerDiv"]'))
      .filter(cell => {
        const cellText = cell.innerText || '';
        return cellText.includes(`@${userHandle}`) && cell.offsetParent !== null;
      });
  }

  function findMoreButtonInCell(cell) {
    const article = cell.querySelector('article');
    if (!article) return null;
    
    const moreBtn = article.querySelector('button[data-testid="caret"][aria-label="More"]');
    return moreBtn && moreBtn.offsetParent !== null ? moreBtn : null;
  }

  function getMenuItems() {
    return Array.from(document.querySelectorAll('div[role="menuitem"][tabindex="0"]'))
      .filter(e => e.offsetParent !== null);
  }

  async function clickDeleteIfPresent() {
    for (let i = 0; i < 15; i++) {
      const items = getMenuItems();
      const deleteItem = items.find(item => {
        const label = (item.innerText || '').trim().toLowerCase();
        return /delete|excluir|apagar/.test(label);
      });

      if (deleteItem) {
        deleteItem.click();
        return true;
      }
      await delay(100);
    }
    return false;
  }

  async function confirmDelete() {
    for (let i = 0; i < 15; i++) {
      const confirmBtn = document.querySelector('[data-testid="confirmationSheetConfirm"]');
      if (confirmBtn && confirmBtn.offsetParent !== null) {
        confirmBtn.click();
        return true;
      }
      await delay(100);
    }
    return false;
  }

  const userHandle = extractHandleFromURL();
  console.log(`🚀 Started for @${userHandle}`);

  let count = 0;
  let scrollAttempts = 0;

  while (true) {
    const cells = findUserReplyCells(userHandle);

    if (cells.length === 0) {
      console.log(`⬇️ Scrolling down ${scrollAttempts + 1}/${maxScrollAttempts}`);
      scrollAttempts++;
      
      if (scrollAttempts >= maxScrollAttempts) {
        console.log('✅ Done');
        break;
      }

      window.scrollBy(0, window.innerHeight * 0.5);
      await delay(1000);
      continue;
    }

    scrollAttempts = 0;

    const cell = cells[0];
    const moreBtn = findMoreButtonInCell(cell);

    if (!moreBtn) {
      console.log('⚠️ No More button found, scrolling...');
      window.scrollBy(0, window.innerHeight * 0.5);
      await delay(1000);
      continue;
    }

    moreBtn.scrollIntoView({ block: 'center' });
    await delay(300);
    moreBtn.click();
    await delay(menuDelay);

    const deleted = await clickDeleteIfPresent();
    
    if (deleted) {
      await delay(300);
      await confirmDelete();
      count++;
      console.log(`✅ Deleted (${count} total)`);
      await delay(clickDelay);
      continue;
    } else {
      console.log(`⏭️ Skipping`);
      document.body.click();
      window.scrollBy(0, window.innerHeight * 0.3);
      await delay(clickDelay);
    }
  }

  console.log(`\n🎉 Finished! Total: ${count}`);
})();
```
