import hudson.matrix.*
import hudson.model.*

hudsonInstance = hudson.model.Hudson.instance
jobNames = hudsonInstance.getJobNames()
allItems = []
for (name in jobNames) {
  allItems += hudsonInstance.getItemByFullName(name)
}

// Iterate over all jobs and find the ones that have a hudson.plugins.git.util.BuildData
// as an action.
//
// We then clean it by removing the useless array action.buildsByBranchName
//

for (job in allItems) {
  println("job: " + job.name);
  def counter = 0;
  for (build in job.getBuilds()) {
    // It is possible for a build to have multiple BuildData actions
    // since we can use the Mulitple SCM plugin.
    def gitActions = build.getActions(hudson.plugins.git.util.BuildData.class)
    echo gitActions.toString()
  }
}