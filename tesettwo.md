const { test, expect } = require('@playwright/test');

test('Check accessibility of the page', async ({ page }) => {
  // Intercept all network requests to disable cache
  await page.route('**/*', (route, request) => {
    // Add Cache-Control header to disable cache for all requests
    route.continue({
      headers: {
        ...request.headers(),
        'Cache-Control': 'no-cache', // Prevent caching
      },
    });
  });

  // Navigate to the webpage you want to test
  await page.goto('https://www.fidelity.com/');

  // Inject axe-core into the page (make sure axe-core is installed as a dependency)
  await page.addScriptTag({ path: require.resolve('axe-core') });

  // Run accessibility checks using axe-core
  const result = await page.evaluate(() => {
    return new Promise((resolve) => {
      window.axe.run(document, {}, (err, results) => {
        if (err) {
          console.error('Axe failed to run:', err);
          resolve([]);
        }
        resolve(results.violations);
      });
    });
  });

  // Log the accessibility violations (if any) to the console for debugging
  console.log(result);

  // Assert that no violations are found
  expect(result.length).toBe(0);
});

