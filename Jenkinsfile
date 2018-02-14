#!/usr/bin/env groovy

@Library('kanolib')
import build_deb_pkg


def repo_name = 'kano-os-recovery'


stage ('Build') {
    autobuild_repo_pkg "$repo_name"
}
