npm init -y
npm install puppeteer
const puppeteer = require('puppeteer');

async function launchBrowser() {
  return await puppeteer.launch({
    headless: true,
    args: ['--no-sandbox', '--disable-setuid-sandbox']
  });
}

module.exports = { launchBrowser };

module.exports = {
  loginUrl: 'https://example.com/login',
  username: process.env.USERNAME || 'testUser',
  password: process.env.PASSWORD || 'testPassword'
};

async function waitAndType(page, selector, text) {
  await page.waitForSelector(selector);
  await page.type(selector, text, { delay: 50 });
}

async function waitAndClick(page, selector) {
  await page.waitForSelector(selector);
  await page.click(selector);
}

async function takeScreenshot(page, name = 'screenshot.png') {
  await page.screenshot({ path: name, fullPage: true });
}

module.exports = { waitAndType, waitAndClick, takeScreenshot };

const { launchBrowser } = require('./browser');
const { waitAndType, waitAndClick, takeScreenshot } = require('./utils');
const config = require('./config');

(async () => {
  const browser = await launchBrowser();
  const page = await browser.newPage();

  try {
    console.log('Starting automation...');

    await page.goto(config.loginUrl, { waitUntil: 'networkidle2' });

    console.log('Entering credentials...');
    await waitAndType(page, '#username', config.username);
    await waitAndType(page, '#password', config.password);

    console.log('Submitting login form...');
    await waitAndClick(page, '#login-button');

    await page.waitForNavigation({ waitUntil: 'networkidle2' });

    console.log('Login successful');

    await takeScreenshot(page, 'dashboard.png');

    console.log('Screenshot saved');

  } catch (error) {
    console.error('Automation failed:', error);
  } finally {
    await browser.close();
  }
})();
