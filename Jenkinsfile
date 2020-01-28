node ('docker') {

  def err = null
  def packer_location = "/usr/local/packer"
  def output_location = "output-ova-vmware-iso"
  def packer_file_name = 'centos7.json'
  def packer_bin = "/usr/local/bin/packer"
  currentBuild.result = "SUCCESS"

  try {

    stage ('Validate') {
      def packer_file = "${packer_location}/${packer_file_name}"
      print "Running packer validate on : ${packer_file}"
      dir (packer_location){
        sh "${packer_bin} -v ; ${packer_bin} validate ${packer_file}"
      }
    }

    stage ('Build') {
      dir (packer_location){
        sh "${packer_bin} build ${packer_file}"
      }

    }

    stage ('Tx to Build Host'){
      print "SCP File to Puppet"
      dir (packer_location) {
        def ova_file = "${packer_location}/${output_location}/test.ova"
        sh "scp ${ova_file} root@zfs:/backups/isos/centos/."
      }
    }

    stage 'Clean Up'
        sh "rm -rf ${packer_location}/${output_location}"
  }

  catch (caughtError) {
    err = caughtError
    currentBuild.result = "FAILURE"
  }

  finally {
    /* Must re-throw exception to propagate error */
    if (err) {
      throw err
    }
  }
}

