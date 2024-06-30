### Description of the Task

In this task, we added support for three additional programming languages (Ruby, Go, and PHP) to an online code judge system. The code judge system evaluates code submissions, compiles, and executes them, returning the results. We also wrote test cases to verify the functionality of the new language support.

### Step-by-Step Process

#### 1. Install Necessary Compilers/Interpreters

##### Ruby
1. Download the Ruby installer from the [official Ruby website](https://rubyinstaller.org/).
2. Run the installer and follow the prompts to complete the installation.
3. Ensure you add Ruby to your system PATH during the installation process.

##### Go
1. Download the Go installer from the [official Go website](https://golang.org/dl/).
2. Run the installer and follow the prompts to complete the installation.
3. Ensure you add Go to your system PATH during the installation process.

##### PHP
1. Download the PHP installer from the [official PHP website](https://windows.php.net/download/).
2. Extract the downloaded ZIP file to a directory of your choice.
3. Add the PHP directory to your system PATH:
   - Right-click on "This PC" or "Computer" on the desktop or in File Explorer.
   - Click on "Properties".
   - Click on "Advanced system settings".
   - Click on "Environment Variables".
   - In the "System variables" section, find the "Path" variable and click on "Edit".
   - Add the path to the PHP directory (e.g., `C:\path\to\php`).
   - Click "OK" to save the changes.

#### 2. Update Configuration Files

Update your project's configuration files to recognize Ruby, Go, and PHP.

```javascript
const languages = {
    'nodejs': {
        'compile': null,
        'execute': 'node'
    },
    'python': {
        'compile': null,
        'execute': 'python'
    },
    'cpp': {
        'compile': 'g++ -o /tmp/a.out',
        'execute': '/tmp/a.out'
    },
    // Add new languages here
    'ruby': {
        'compile': null,
        'execute': 'ruby'
    },
    'go': {
        'compile': 'go build -o /tmp/a.out',
        'execute': '/tmp/a.out'
    },
    'php': {
        'compile': null,
        'execute': 'php'
    }
};
```

#### 3. Modify the Backend

Modify your execution logic to handle the new languages.

```javascript
const { exec } = require('child_process');
const fs = require('fs');

app.post('/api/execute', (req, res) => {
    const { language, script } = req.body;
    const langConfig = languages[language];

    if (!langConfig) {
        return res.status(400).json({ error: 'Unsupported language' });
    }

    const scriptPath = `C:\\tmp\\script.${language}`;
    fs.writeFileSync(scriptPath, script);

    let compileCommand = langConfig.compile ? `${langConfig.compile} ${scriptPath}` : null;
    let executeCommand = `${langConfig.execute} ${scriptPath}`;

    const runCommand = compileCommand ? `${compileCommand} && ${executeCommand}` : executeCommand;

    exec(runCommand, (error, stdout, stderr) => {
        if (error) {
            return res.status(500).json({ error: stderr });
        }
        res.json({ output: stdout });
    });
});
```

#### 4. Add Tests for New Languages

Write test cases to ensure that code execution for Ruby, Go, and PHP works correctly.

```javascript
const request = require('supertest');
const app = require('../app'); // Adjust the path as necessary
const { describe, expect, it } = require('@jest/globals');

const ENDPOINT = process.env.ENDPOINT || 'http://localhost:3000/api/execute/'

describe('Code Execution Tests', () => {
    it('should execute Ruby code', (done) => {
        request(app)
            .post(ENDPOINT)
            .send({ language: 'ruby', script: 'puts "hello world"' })
            .expect(200)
            .expect((res) => {
                if (!res.body.output.includes('hello world')) throw new Error('Output mismatch');
            })
            .end(done);
    });

    it('should execute Go code', (done) => {
        request(app)
            .post(ENDPOINT)
            .send({ language: 'go', script: 'package main; import "fmt"; func main() { fmt.Println("hello world") }' })
            .expect(200)
            .expect((res) => {
                if (!res.body.output.includes('hello world')) throw new Error('Output mismatch');
            })
            .end(done);
    });

    it('should execute PHP code', (done) => {
        request(app)
            .post(ENDPOINT)
            .send({ language: 'php', script: '<?php echo "hello world"; ?>' })
            .expect(200)
            .expect((res) => {
                if (!res.body.output.includes('hello world')) throw new Error('Output mismatch');
            })
            .end(done);
    });
});
```

### Running the Tests

Run your tests using Jest to verify that everything works correctly.

```bash
npm test
```

or

```bash
npx jest
```

### Summary

1. **Install Necessary Compilers/Interpreters**: Use appropriate installers for Ruby, Go, and PHP on Windows.
2. **Update Configuration**: Add support for the new languages in your project's configuration files.
3. **Modify Backend**: Update your backend logic to handle code execution for the new languages.
4. **Add Tests**: Write tests to ensure the new language support is working correctly.
5. **Run Tests**: Execute the tests using Jest to verify the implementation.

By following these steps, you can successfully add support for Ruby, Go, and PHP to your code judge project and ensure their functionality through automated tests.
