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

## Post Removal Tool

Removes all posts you made.
Run this code while in: https://x.com/YOURPROFILE



