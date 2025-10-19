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

```javascript
// Mass post removal
// Paste on Browser Console (F12) and press enter

(async () => {
  // Configuration
  const delay = ms => new Promise(r => setTimeout(r, ms));
  const clickDelay = 800;        // Delay between actions (ms)
  const menuDelay = 600;         // Delay for menu to appear (ms)
  const maxScrollAttempts = 5;   // Max scrolls without finding new posts

  /**
   * Finds all "More" buttons in posts within article elements
   * Filters out hidden elements and non-post buttons
   * @returns {Array} Array of More buttons
   */
  function findMoreButtons() {
    return Array.from(document.querySelectorAll('article button[data-testid="caret"][aria-label="More"]'))
      .filter(b => b.offsetParent !== null);
  }

  /**
   * Gets all visible menu items from the dropdown
   * @returns {Array} Array of menu item elements
   */
  function getMenuItems() {
    return Array.from(document.querySelectorAll('div[role="menuitem"][tabindex="0"]'))
      .filter(e => e.offsetParent !== null);
  }

  /**
   * Looks for and clicks the "Delete" option in the menu
   * If found, returns true; otherwise returns false (indicating a retweet)
   * @returns {Promise<boolean>} true if delete option was found and clicked
   */
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

  /**
   * Confirms the deletion by clicking the confirmation button
   * Retries up to 15 times with 100ms delay between attempts
   * @returns {Promise<boolean>} true if confirmation button was found and clicked
   */
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

  // Main script logic
  let count = 0;                 // Posts deleted counter
  let currentIndex = 0;          // Current post index in the list
  let scrollAttempts = 0;        // Consecutive scroll attempts without new posts

  console.log('🚀 Twitter/X Post Deleter started');

  while (true) {
    const btns = findMoreButtons();

    // Case 1: No posts found - scroll and try again
    if (btns.length === 0) {
      console.log(`📜 No posts found. Scroll attempt ${scrollAttempts + 1}/${maxScrollAttempts}`);
      scrollAttempts++;
      
      if (scrollAttempts >= maxScrollAttempts) {
        console.log('✅ Reached scroll limit. No more posts to delete.');
        break;
      }

      window.scrollBy(0, window.innerHeight * 0.5);
      await delay(1000);
      continue;
    }

    // Case 2: Current index is beyond the list length - reached end of visible posts
    if (currentIndex >= btns.length) {
      console.log(`📜 End of post list. Scroll attempt ${scrollAttempts + 1}/${maxScrollAttempts}`);
      scrollAttempts++;
      
      if (scrollAttempts >= maxScrollAttempts) {
        console.log('✅ Reached scroll limit. No more posts to delete.');
        break;
      }

      window.scrollBy(0, window.innerHeight * 0.5);
      await delay(1000);
      continue;
    }

    // Reset scroll attempts when we successfully process posts
    scrollAttempts = 0;

    // Get the current post's More button and attempt deletion
    const btn = btns[currentIndex];
    btn.scrollIntoView({ block: 'center' });
    await delay(300);
    btn.click();
    await delay(menuDelay);

    const deleted = await clickDeleteIfPresent();
    
    if (deleted) {
      // Successfully found delete option - confirm the deletion
      await delay(300);
      await confirmDelete();
      count++;
      console.log(`✅ Post deleted (${count} total)`);
      // Note: Do NOT increment currentIndex - the next post moves up to this position
    } else {
      // No delete option found - this is a retweet, skip it
      console.log(`⏭️  Retweet detected, skipping to next post`);
      document.body.click(); // Close the menu
      currentIndex++; // Move to next post
    }

    await delay(clickDelay);
  }

  console.log(`\n🎉 Script finished! Total posts deleted: ${count}`);
})();
```

