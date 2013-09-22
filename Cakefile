fs      = require 'fs'
path    = require 'path'
async   = require 'async'
{print} = require 'util'
{spawn} = require 'child_process'

testCodes  = []

task 'build', 'Build project', ->
  build()

task 'test', 'Run tests', ->
  build -> test()

build = (callback) ->
  builder = (args...) ->
    (callback) ->
      coffeeCmd = 'coffee' + if process.platform is 'win32' then '.cmd' else ''
      coffee = spawn coffeeCmd, args
      coffee.stderr.on 'data', (data) -> process.stderr.write data.toString()
      coffee.stdout.on 'data', (data) -> print data.toString()
      coffee.on 'exit', (code) -> callback?(code,code)
  async.parallel [
    builder('-c', '-o', 'test/lib', 'test/src')
  ], (err, results) -> callback?() unless err

test = ->
  tester = (file) ->
    (callback) ->
      mochaCmd = 'mocha' + if process.platform is 'win32' then '.cmd' else ''
      mocha = spawn mochaCmd, ['-u', 'bdd', '-R', 'spec', '-t', '20000', '--colors', "test/lib/#{file}"],
        stdio: 'inherit'
      mocha.on 'exit', (code) -> callback?(code,code)
  testFiles = ['mocha-phantomjs.js']
  testers = (tester file for file in testFiles)
  async.series testers, (err, results) -> 
    passed = results.every (code) -> code is 0
    process.exit if passed then 0 else 1



