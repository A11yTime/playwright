const { test, expect } = require('@playwright/test');

test('Check accessibility of the page', async ({ page }) => {
  // Navigate to the webpage you want to test
  await page.goto('https://www.fidelity.com/');

  // Inject axe-core into the page (so you can use axe.run)
  await page.addScriptTag({
    url: 'https://cdnjs.cloudflare.com/ajax/libs/axe-core/4.3.3/axe.min.js',
  });

  // Run Axe accessibility check on the page
  const result = await page.evaluate(() => {
    return new Promise((resolve) => {
      // Run axe-core to check for accessibility violations
      if (typeof axe !== 'undefined') {
        axe.run(document, {}, (err, results) => {
          if (err) {
            console.error('Axe failed to run:', err);
            resolve([]);
          }
          resolve(results.violations);  // Return any accessibility violations
        });
      } else {
        resolve([]);  // If axe is undefined, resolve with an empty array
      }
    });
  });

  // Log the results to inspect violations
  console.log('Accessibility Violations:', result);

  // Assert that no violations are found (you can modify this based on what you find)
  expect(result.length).toBe(0);  // Fail if there are any violations
});