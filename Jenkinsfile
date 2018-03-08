stage 'CI'
node {

    checkout scm
    
    //git branch: 'jenkins2-course', 
    //    url: 'https://github.com/g0t4/solitaire-systemjs-course'
        
    bat 'npm install'
    
    stash name: 'everything', 
          excludes: 'test-results/**', 
          includes: '**'
          
    bat 'npm run test-single-run -- --browsers PhantomJS'
    
    step([$class: 'JUnitResultArchiver', 
          testResults: 'test-results/**/test-results.xml'])
    
}

node('windows') {
    bat 'dir'
    
    bat 'del /S /Q *'
    
    unstash 'everything'
    
    bat 'dir'
}

stage 'Browser Testing'
parallel chrome: {
    runTests("Chrome")
}, firefox: {
    runTests("Firefox")
} 

def runTests(browser) {
    node {
        bat 'del /S /Q *'

        unstash 'everything'

        bat "npm run test-single-run -- --browsers ${browser}"

        step([$class: 'JUnitResultArchiver', 
              testResults: 'test-results/**/test-results.xml'])
    }
}

input 'Deploy to Staging?'

stage name: 'Deploy to staging', concurrency: 1
node {
    // write build number to index page so we can see this update
    bat "echo '${env.BUILD_DISPLAY_NAME}' >> app/index.html"
    
    // deploy to a docker container mapped to port 3000
    // on windows use: bat 'docker-compose up -d --build'
    bat 'docker-compose up -d --build'
}